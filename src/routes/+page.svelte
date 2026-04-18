<script lang="ts">
	import { animate } from 'animejs';

	type BoxType = 'project' | 'db';

	interface Box {
		id: number;
		x: number;
		y: number;
		w: number;
		h: number;
		nombre: string;
		tipo: BoxType;
	}

	type Slot = 'left' | 'top' | 'right' | 'bottom';

	interface Connection {
		fromId: number;
		toId: number;
		fromSlot: Slot;
		toSlot: Slot;
		labels: string[];
	}

	let boxes = $state<Box[]>([]);

	let connections = $state<Connection[]>([]);
	let nextId = 1;

	// Undo history
	let history: string[] = [];
	const MAX_HISTORY = 50;

	function saveSnapshot() {
		history.push(JSON.stringify({ boxes, connections, nextId, actionRefs }));
		if (history.length > MAX_HISTORY) history.shift();
	}

	function undo() {
		if (history.length === 0) return;
		const snap = JSON.parse(history.pop()!);
		boxes = snap.boxes;
		connections = snap.connections;
		nextId = snap.nextId;
		if (snap.actionRefs) actionRefs = snap.actionRefs;
	}

	function createBox(tipo: BoxType = 'project') {
		saveSnapshot();
		const id = nextId++;
		const offset = (boxes.length % 5) * 30;
		const defaults = tipo === 'db'
			? { w: 130, h: 130 }
			: { w: 130, h: 125 };
		boxes.push({ id, x: 80 + offset, y: 80 + offset, ...defaults, nombre: '', tipo });
	}

	// Box dragging
	let draggingId = $state<number | null>(null);
	let offsetX = 0;
	let offsetY = 0;
	let boxEls: Record<number, HTMLDivElement> = {};
	let workspaceEl: HTMLDivElement;

	// Multi-select
	let selectedIds = $state<Set<number>>(new Set());
	let dragOffsets: Record<number, { dx: number; dy: number }> = {};

	// Lasso selection
	let lassoActive = $state(false);
	let lassoStart = { x: 0, y: 0 };
	let lassoEnd = $state({ x: 0, y: 0 });

	// Wire dragging
	let wiringFromId = $state<number | null>(null);
	let wiringFromSlot = $state<Slot>('right');
	let wireEnd = $state({ x: 0, y: 0 });

	const BOX_SIZE = 120;
	const HALF = BOX_SIZE / 2;
	const DOT_OFFSET = 6;

	function clamp(val: number, min: number, max: number) {
		return Math.max(min, Math.min(max, val));
	}

	function getDotPos(box: Box, slot: Slot) {
		switch (slot) {
			case 'left': return { x: box.x - DOT_OFFSET, y: box.y + box.h / 2 };
			case 'top': return { x: box.x + box.w / 2, y: box.y - DOT_OFFSET };
			case 'right': return { x: box.x + box.w + DOT_OFFSET, y: box.y + box.h / 2 };
			case 'bottom': return { x: box.x + box.w / 2, y: box.y + box.h + DOT_OFFSET };
		}
	}

	function buildCurve(x1: number, y1: number, x2: number, y2: number) {
		const dx = x2 - x1;
		const tension = Math.max(Math.abs(dx) * 0.4, 50);
		return `M ${x1} ${y1} C ${x1 + tension} ${y1}, ${x2 - tension} ${y2}, ${x2} ${y2}`;
	}

	// Box drag handlers
	function onPointerDown(e: PointerEvent, box: Box) {
		if (e.button !== 0) return;
		if (wiringFromId !== null) return;
		if (resizingId !== null) return;
		e.stopPropagation();

		// If clicking an unselected box, clear selection
		if (!selectedIds.has(box.id)) {
			selectedIds = new Set();
		}

		saveSnapshot();
		draggingId = box.id;
		const target = e.currentTarget as HTMLElement;
		const boxRect = target.getBoundingClientRect();
		offsetX = e.clientX - boxRect.left;
		offsetY = e.clientY - boxRect.top;
		target.setPointerCapture(e.pointerId);

		// Store offsets for all selected boxes relative to the dragged box
		if (selectedIds.size > 0 && selectedIds.has(box.id)) {
			for (const sid of selectedIds) {
				const sb = boxes.find((b) => b.id === sid);
				if (sb) {
					dragOffsets[sid] = { dx: sb.x - box.x, dy: sb.y - box.y };
				}
			}
		}

		animate(boxEls[box.id], {
			scale: [1, 1.08],
			boxShadow: [
				'0 4px 20px rgba(0,0,0,0.3)',
				'0 16px 50px rgba(0,0,0,0.6)'
			],
			duration: 250,
			ease: 'outCubic'
		});
	}

	function onPointerMove(e: PointerEvent, box: Box) {
		if (draggingId !== box.id) return;
		const wsRect = workspaceEl.getBoundingClientRect();
		const newX = clamp(e.clientX - wsRect.left - offsetX, 0, wsRect.width - box.w);
		const newY = clamp(e.clientY - wsRect.top - offsetY, 0, wsRect.height - box.h);

		// Move selected boxes together
		if (selectedIds.size > 0 && selectedIds.has(box.id)) {
			for (const sid of selectedIds) {
				const sb = boxes.find((b) => b.id === sid);
				const off = dragOffsets[sid];
				if (sb && off) {
					sb.x = clamp(newX + off.dx, 0, wsRect.width - sb.w);
					sb.y = clamp(newY + off.dy, 0, wsRect.height - sb.h);
				}
			}
		} else {
			box.x = newX;
			box.y = newY;
		}
	}

	function onPointerUp(box: Box) {
		if (draggingId !== box.id) return;
		draggingId = null;
		dragOffsets = {};
		animate(boxEls[box.id], {
			scale: [1.15, 1],
			boxShadow: [
				'0 12px 40px rgba(0,0,0,0.6)',
				'0 4px 20px rgba(0,0,0,0.3)'
			],
			duration: 400,
			ease: 'outElastic(1, 0.5)'
		});
	}

	// Wire drag handlers
	function onDotPointerDown(e: PointerEvent, box: Box, slot: Slot) {
		if (e.button !== 0) return;
		e.stopPropagation();
		wiringFromId = box.id;
		wiringFromSlot = slot;
		const wsRect = workspaceEl.getBoundingClientRect();
		wireEnd = { x: e.clientX - wsRect.left, y: e.clientY - wsRect.top };
	}

	function onWorkspacePointerMove(e: PointerEvent) {
		if (lassoActive) {
			const wsRect = workspaceEl.getBoundingClientRect();
			lassoEnd = { x: e.clientX - wsRect.left, y: e.clientY - wsRect.top };
			return;
		}
		if (wiringFromId === null) return;
		const wsRect = workspaceEl.getBoundingClientRect();
		wireEnd = { x: e.clientX - wsRect.left, y: e.clientY - wsRect.top };
	}

	function onDotPointerUp(e: PointerEvent, targetBox: Box, slot: Slot) {
		if (wiringFromId === null) return;
		e.stopPropagation();
		if (wiringFromId !== targetBox.id) {
			const exists = connections.some(
				(c) => c.fromId === wiringFromId && c.toId === targetBox.id && c.fromSlot === wiringFromSlot && c.toSlot === slot
			);
			if (!exists) {
					saveSnapshot();
					connections.push({ fromId: wiringFromId!, toId: targetBox.id, fromSlot: wiringFromSlot, toSlot: slot, labels: [''] });
			}
		}
		wiringFromId = null;
	}

	function onWorkspacePointerDown(e: PointerEvent) {
		if (e.button !== 0) return;
		if (wiringFromId !== null) return;
		if (draggingId !== null) return;
		const wsRect = workspaceEl.getBoundingClientRect();
		const x = e.clientX - wsRect.left;
		const y = e.clientY - wsRect.top;
		lassoActive = true;
		lassoStart = { x, y };
		lassoEnd = { x, y };
		selectedIds = new Set();
		workspaceEl.setPointerCapture(e.pointerId);
	}

	function onWorkspacePointerUp(e: PointerEvent) {
		if (lassoActive) {
			const x1 = Math.min(lassoStart.x, lassoEnd.x);
			const y1 = Math.min(lassoStart.y, lassoEnd.y);
			const x2 = Math.max(lassoStart.x, lassoEnd.x);
			const y2 = Math.max(lassoStart.y, lassoEnd.y);
			const newSel = new Set<number>();
			for (const box of boxes) {
				if (box.x + box.w > x1 && box.x < x2 && box.y + box.h > y1 && box.y < y2) {
					newSel.add(box.id);
				}
			}
			selectedIds = newSel;
			lassoActive = false;
			workspaceEl.releasePointerCapture(e.pointerId);
		}
		wiringFromId = null;
	}

	let dotEls: Record<string, HTMLSpanElement> = {};

	function onDotEnter(box: Box, slot: string) {
		if (wiringFromId === null || wiringFromId === box.id) return;
		const key = `${box.id}-${slot}`;
		animate(dotEls[key], {
			scale: [1, 1.8],
			boxShadow: ['0 0 6px rgba(100,149,237,0.6)', '0 0 14px rgba(100,149,237,0.9)'],
			duration: 200,
			ease: 'outCubic'
		});
	}

	function onDotLeave(box: Box, slot: string) {
		const key = `${box.id}-${slot}`;
		if (!dotEls[key]) return;
		animate(dotEls[key], {
			scale: [1.8, 1],
			boxShadow: ['0 0 14px rgba(100,149,237,0.9)', '0 0 6px rgba(100,149,237,0.6)'],
			duration: 200,
			ease: 'outCubic'
		});
	}

	// Remove box
	let confirmingBox = $state<Box | null>(null);
	let confirmPos = $state({ x: 0, y: 0 });

	function removeBox(e: MouseEvent, box: Box) {
		e.preventDefault();
		e.stopPropagation();
		confirmPos = { x: e.clientX, y: e.clientY };
		confirmingBox = box;
	}

	function confirmRemoveBox() {
		if (!confirmingBox) return;
		saveSnapshot();
		for (let i = connections.length - 1; i >= 0; i--) {
			if (connections[i].fromId === confirmingBox.id || connections[i].toId === confirmingBox.id) {
				connections.splice(i, 1);
			}
		}
		const idx = boxes.findIndex((b) => b.id === confirmingBox.id);
		if (idx !== -1) boxes.splice(idx, 1);
		confirmingBox = null;
	}

	function cancelRemoveBox() {
		confirmingBox = null;
	}

	// Remove connection
	function removeConnection(index: number) {
		saveSnapshot();
		connections.splice(index, 1);
	}

	function removeConnectionsForDot(boxId: number, slot: Slot) {
		saveSnapshot();
		for (let i = connections.length - 1; i >= 0; i--) {
			if ((connections[i].fromId === boxId && connections[i].fromSlot === slot) ||
				(connections[i].toId === boxId && connections[i].toSlot === slot)) {
				connections.splice(i, 1);
			}
		}
	}

	function exportWorkflow() {
		if (actionRefs.length === 0 && connections.length > 0) buildActionLines();
		const data = { boxes, connections, nextId, actionRefs };
		const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
		const url = URL.createObjectURL(blob);
		const a = document.createElement('a');
		a.href = url;
		a.download = 'workflow.json';
		a.click();
		URL.revokeObjectURL(url);
	}

	function importWorkflow() {
		const input = document.createElement('input');
		input.type = 'file';
		input.accept = '.json';
		input.onchange = () => {
			const file = input.files?.[0];
			if (!file) return;
			const reader = new FileReader();
			reader.onload = () => {
				try {
					const data = JSON.parse(reader.result as string);
								boxes = (data.boxes ?? []).map((b: Record<string, unknown>) => ({ ...b, w: b.w ?? 130, h: b.h ?? 125, nombre: b.nombre ?? '', tipo: b.tipo ?? 'project' }));
					connections = (data.connections ?? []).map((c: Record<string, unknown>) => ({ ...c, fromSlot: c.fromSlot ?? 'right', toSlot: c.toSlot ?? 'left', labels: Array.isArray(c.labels) ? c.labels : (c.label ? [c.label] : ['']) }));
					nextId = data.nextId ?? 1;
					if (Array.isArray(data.actionRefs) && data.actionRefs.length > 0) {
						actionRefs = data.actionRefs;
						showActionsList = true;
						labelsHidden = false;
					} else if (connections.length > 0) {
						buildActionLines();
						showActionsList = true;
						labelsHidden = false;
					}
				} catch {
					console.error('JSON inválido');
				}
			};
			reader.readAsText(file);
		};
		input.click();
	}

	// Resize
	let resizingId = $state<number | null>(null);
	let resizeStartX = 0;
	let resizeStartY = 0;
	let resizeStartW = 0;
	let resizeStartH = 0;
	const MIN_SIZE = 80;

	function onResizePointerDown(e: PointerEvent, box: Box) {
		if (e.button !== 0) return;
		e.stopPropagation();
		saveSnapshot();
		resizingId = box.id;
		resizeStartX = e.clientX;
		resizeStartY = e.clientY;
		resizeStartW = box.w;
		resizeStartH = box.h;
		(e.currentTarget as HTMLElement).setPointerCapture(e.pointerId);
	}

	function onResizePointerMove(e: PointerEvent, box: Box) {
		if (resizingId !== box.id) return;
		box.w = Math.max(MIN_SIZE, resizeStartW + (e.clientX - resizeStartX));
		box.h = Math.max(MIN_SIZE, resizeStartH + (e.clientY - resizeStartY));
	}

	function onResizePointerUp(box: Box) {
		if (resizingId !== box.id) return;
		resizingId = null;
	}

	// Derived paths
	let connectionData = $derived.by(() => {
		return connections.map((conn) => {
			const fromBox = boxes.find((b) => b.id === conn.fromId)!;
			const toBox = boxes.find((b) => b.id === conn.toId)!;
			const from = getDotPos(fromBox, conn.fromSlot);
			const to = getDotPos(toBox, conn.toSlot);
			return {
				d: buildCurve(from.x, from.y, to.x, to.y),
				mx: (from.x + to.x) / 2,
				my: (from.y + to.y) / 2
			};
		});
	});

	let draggingWirePath = $derived.by(() => {
		if (wiringFromId === null) return '';
		const fromBox = boxes.find((b) => b.id === wiringFromId)!;
		const from = getDotPos(fromBox, wiringFromSlot);
		return buildCurve(from.x, from.y, wireEnd.x, wireEnd.y);
	});

	// Actions list
	let showActionsList = $state(false);
	let actionRefs = $state<{ fromId: number; toId: number; labelIdx: number; flipped: boolean }[]>([]);
	let draggingRowIdx = $state<number | null>(null);
	let dragOverRowIdx = $state<number | null>(null);
	let actionsPanelEl = $state<HTMLDivElement>(null!);
	let actionsContainerWidth = $state(0);
	let labelsHidden = $state(false);

	function buildActionLines() {
		interface SortableRef { fromId: number; toId: number; labelIdx: number; sortX: number; sortY: number; }
		const items: SortableRef[] = connections.flatMap((conn) => {
			const fromBox = boxes.find((b) => b.id === conn.fromId);
			const toBox = boxes.find((b) => b.id === conn.toId);
			if (!fromBox || !toBox) return [] as SortableRef[];
			const labels = conn.labels.map((l, idx) => ({ l, idx })).filter(({ l }) => l.trim() !== '');
			if (labels.length === 0) return [{ fromId: conn.fromId, toId: conn.toId, labelIdx: -1, sortX: fromBox.x, sortY: fromBox.y }];
			return labels.map(({ idx }) => ({ fromId: conn.fromId, toId: conn.toId, labelIdx: idx, sortX: fromBox.x, sortY: fromBox.y }));
		});
		items.sort((a, b) => a.sortX - b.sortX || a.sortY - b.sortY);
		actionRefs = items.map(({ fromId, toId, labelIdx }) => ({ fromId, toId, labelIdx, flipped: false }));
	}

	let actionDisplay = $derived.by(() => {
		return actionRefs.map((ref) => {
			const fromBox = boxes.find((b) => b.id === ref.fromId);
			const toBox = boxes.find((b) => b.id === ref.toId);
			const fromName = fromBox?.nombre || `nodo_${ref.fromId}`;
			const toName = toBox?.nombre || `nodo_${ref.toId}`;
			let label: string | null = null;
			if (ref.labelIdx >= 0) {
				const conn = connections.find((c) => c.fromId === ref.fromId && c.toId === ref.toId);
				label = conn?.labels[ref.labelIdx] || null;
			}
			const from = ref.flipped ? toName : fromName;
			const to = ref.flipped ? fromName : toName;
			return { from, label, to };
		});
	});

	function flipRow(idx: number) {
		saveSnapshot();
		actionRefs[idx].flipped = !actionRefs[idx].flipped;
		actionRefs = [...actionRefs];
	}

	function toggleActionsList() {
		showActionsList = true;
		labelsHidden = false;
		buildActionLines();
	}

	function clearLabels() {
		labelsHidden = true;
		revealedLabels = new Set();
	}

	let usedDots = $derived.by(() => {
		const set = new Set<string>();
		for (const conn of connections) {
			set.add(`${conn.fromId}-${conn.fromSlot}`);
			set.add(`${conn.toId}-${conn.toSlot}`);
		}
		return set;
	});

	// Animation state
	let animatingConnIdx = $state<number | null>(null);
	let animFlipped = $state(false);
	let animProgress = $state(0);
	let animDashOffset = $state(0);
	let animRunning = $state(false);
	let loopAnimation = $state(false);
	let revealedLabels = $state<Set<string>>(new Set());
	let currentAnim: ReturnType<typeof animate> | null = null;

	function runSingleAnimation(connIdx: number, labelIdx: number, flipped: boolean): Promise<void> {
		return new Promise((resolve) => {
			if (!animRunning) { resolve(); return; }
			const conn = connections[connIdx];
			if (!conn) { resolve(); return; }
			const fromBox = boxes.find(b => b.id === conn.fromId);
			const toBox = boxes.find(b => b.id === conn.toId);
			if (!fromBox || !toBox) { resolve(); return; }

			animatingConnIdx = connIdx;
			animFlipped = flipped;
			animProgress = 0;
			animDashOffset = 0;
			if (labelIdx >= 0) {
				revealedLabels = new Set([...revealedLabels, `${connIdx}:${labelIdx}`]);
			} else {
				// No specific label — reveal all labels for this connection
				const newSet = new Set(revealedLabels);
				conn.labels.forEach((_, j) => newSet.add(`${connIdx}:${j}`));
				revealedLabels = newSet;
			}

			const proxy = { progress: 0, dashOffset: 0 };
			currentAnim = animate(proxy, {
				progress: 1,
				dashOffset: -200,
				duration: 1500,
				easing: 'easeInOutQuad',
				onUpdate: () => {
					animProgress = proxy.progress;
					animDashOffset = proxy.dashOffset;
				},
				onComplete: () => {
					animatingConnIdx = null;
					currentAnim = null;
					resolve();
				}
			});
		});
	}

	async function runAllAnimations() {
		if (animRunning || actionRefs.length === 0) return;
		clearLabels();
		animRunning = true;
		do {
			revealedLabels = new Set();
			for (const ref of actionRefs) {
				if (!animRunning) break;
				const connIdx = connections.findIndex(c => c.fromId === ref.fromId && c.toId === ref.toId);
				if (connIdx === -1) continue;
				await runSingleAnimation(connIdx, ref.labelIdx, ref.flipped);
			}
		} while (loopAnimation && animRunning);
		animRunning = false;
	}

	// Get point along a cubic bezier at parameter t
	function bezierPoint(t: number, p0: number, p1: number, p2: number, p3: number) {
		const u = 1 - t;
		return u * u * u * p0 + 3 * u * u * t * p1 + 3 * u * t * t * p2 + t * t * t * p3;
	}

	let animCirclePos = $derived.by(() => {
		if (animatingConnIdx === null) return null;
		const conn = connections[animatingConnIdx];
		if (!conn) return null;
		const fromBox = boxes.find(b => b.id === conn.fromId);
		const toBox = boxes.find(b => b.id === conn.toId);
		if (!fromBox || !toBox) return null;
		const from = getDotPos(fromBox, conn.fromSlot);
		const to = getDotPos(toBox, conn.toSlot);
		const dx = to.x - from.x;
		const tension = Math.max(Math.abs(dx) * 0.4, 50);
		const p0x = from.x, p0y = from.y;
		const p1x = from.x + tension, p1y = from.y;
		const p2x = to.x - tension, p2y = to.y;
		const p3x = to.x, p3y = to.y;
		const t = animFlipped ? 1 - animProgress : animProgress;
		return {
			x: bezierPoint(t, p0x, p1x, p2x, p3x),
			y: bezierPoint(t, p0y, p1y, p2y, p3y)
		};
	});

	function onRowDragStart(e: DragEvent, idx: number) {
		draggingRowIdx = idx;
		if (e.dataTransfer) {
			e.dataTransfer.effectAllowed = 'move';
			e.dataTransfer.setData('text/plain', String(idx));

			const item = actionDisplay[idx];
			const ghost = document.createElement('div');
			ghost.textContent = `${item.from} ${item.label ? '— ' + item.label + ' —' : '→'} ${item.to}`;
			ghost.style.cssText = 'position:fixed;top:-1000px;padding:6px 14px;background:#4a148c;color:white;border-radius:6px;font-size:12px;white-space:nowrap;box-shadow:0 4px 12px rgba(0,0,0,0.3);';
			document.body.appendChild(ghost);
			e.dataTransfer.setDragImage(ghost, ghost.offsetWidth / 2, ghost.offsetHeight / 2);
			requestAnimationFrame(() => document.body.removeChild(ghost));
		}
	}

	function onRowDragOver(e: DragEvent, idx: number) {
		e.preventDefault();
		if (e.dataTransfer) e.dataTransfer.dropEffect = 'move';
		dragOverRowIdx = idx;
	}

	function onRowDrop(e: DragEvent, idx: number) {
		e.preventDefault();
		if (draggingRowIdx !== null && draggingRowIdx !== idx) {
			saveSnapshot();
			const item = actionRefs.splice(draggingRowIdx, 1)[0];
			actionRefs.splice(idx, 0, item);
			actionRefs = [...actionRefs];
		}
		draggingRowIdx = null;
		dragOverRowIdx = null;
	}

	function onRowDragEnd() {
		draggingRowIdx = null;
		dragOverRowIdx = null;
	}

	function handleKeydown(e: KeyboardEvent) {
		if (e.ctrlKey && e.key === 'z') {
			e.preventDefault();
			undo();
		}
	}
</script>

<svelte:window onkeydown={handleKeydown} />

<div class="io-buttons">
	<button class="btn-io" onclick={exportWorkflow} title="Exportar workflow">⬇ Exportar</button>
	<button class="btn-io" onclick={importWorkflow} title="Importar workflow">⬆ Importar</button>
</div>

<div class="create-buttons">
	<button class="btn-create" onclick={() => createBox('project')} title="Crear Proyecto">
		<div class="btn-box">
			<span class="btn-dots"><span class="bd r"></span><span class="bd y"></span><span class="bd g"></span></span>
		</div>
	</button>
	<button class="btn-create" onclick={() => createBox('db')} title="Crear Base de Datos">
		<div class="btn-cylinder">
			<div class="bc-top"></div>
			<div class="bc-body"></div>
			<div class="bc-bottom"></div>
		</div>
	</button>
</div>

<div
	class="workspace"
	bind:this={workspaceEl}
	style={showActionsList ? `left: 2%; width: calc(98% - ${actionsContainerWidth + 40}px);` : ''}
	onpointerdown={onWorkspacePointerDown}
	onpointermove={onWorkspacePointerMove}
	onpointerup={onWorkspacePointerUp}
	oncontextmenu={(e) => e.preventDefault()}
>
	<svg class="connections">
		{#each connectionData as conn, i}
			<path d={conn.d} fill="none" stroke="transparent" stroke-width="12"
				oncontextmenu={(e) => { e.preventDefault(); removeConnection(i); }} />
			<path d={conn.d} fill="none" stroke="white" stroke-width="1.5" pointer-events="none" />
			{#each connections[i].labels as _, j}
			{#if !labelsHidden || revealedLabels.has(`${i}:${j}`)}
			<foreignObject x={conn.mx - 60} y={conn.my - 14 - (connections[i].labels.length - j) * 22} width="120" height="20">
					<div class="conn-label-wrap">
						<input
							class="conn-label"
							type="text"
							bind:value={connections[i].labels[j]}
							size={Math.max(4, connections[i].labels[j].length || 1)}
							onkeydown={(e) => {
								if (e.key === 'Enter' && e.shiftKey) {
									e.preventDefault();
									connections[i].labels = [...connections[i].labels, ''];
								} else if (e.key === 'Enter') {
									e.preventDefault();
									(e.target as HTMLElement).blur();
								} else if (e.key === 'Backspace' && connections[i].labels[j] === '' && connections[i].labels.length > 1) {
									e.preventDefault();
									connections[i].labels = connections[i].labels.filter((_: string, k: number) => k !== j);
								}
							}}
						/>
					</div>
				</foreignObject>
			{/if}
			{/each}
		{/each}
		{#if draggingWirePath}
			<path d={draggingWirePath} fill="none" stroke="white" stroke-width="1.5" stroke-dasharray="6 4" opacity="0.7" />
		{/if}
		{#if animatingConnIdx !== null}
			<path
				d={connectionData[animatingConnIdx]?.d ?? ''}
				fill="none"
				stroke="#ff1744"
				stroke-width="2"
				stroke-dasharray="8 6"
				stroke-dashoffset={animDashOffset}
				pointer-events="none"
			/>
			{#if animCirclePos}
				<circle cx={animCirclePos.x} cy={animCirclePos.y} r="6" fill="#ff1744" pointer-events="none">
				</circle>
			{/if}
		{/if}
	</svg>
	{#each boxes as box (box.id)}
		<div
			class="box"
			class:box-project={box.tipo === 'project'}
			class:box-db={box.tipo === 'db'}
			class:dragging={draggingId === box.id}
			class:selected={selectedIds.has(box.id)}
			style="left: {box.x}px; top: {box.y}px; width: {box.w}px; height: {box.h}px; z-index: {box.id};"
			bind:this={boxEls[box.id]}
			onpointerdown={(e) => onPointerDown(e, box)}
			onpointermove={(e) => onPointerMove(e, box)}
			onpointerup={() => onPointerUp(box)}
			oncontextmenu={(e) => removeBox(e, box)}
		>
			{#if box.tipo === 'project'}
				<div class="window-bar">
					<span class="win-dot wd-r"></span>
					<span class="win-dot wd-y"></span>
					<span class="win-dot wd-g"></span>
				</div>
				<input
					class="box-label label-project"
					type="text"
					bind:value={box.nombre}
					onpointerdown={(e) => e.stopPropagation()}
				/>
			{:else if box.tipo === 'db'}
				<div class="cyl-top"></div>
				<div class="cyl-body">
					<input
						class="box-label label-db"
						type="text"
						bind:value={box.nombre}
						onpointerdown={(e) => e.stopPropagation()}
					/>
				</div>
				<div class="cyl-bottom"></div>
			{:else}
				<input
					class="box-label"
					type="text"
					bind:value={box.nombre}
					onpointerdown={(e) => e.stopPropagation()}
				/>
			{/if}
			<span
				class="dot dot-left"
				bind:this={dotEls[`${box.id}-left`]}
				onpointerdown={(e) => onDotPointerDown(e, box, 'left')}
				onpointerup={(e) => onDotPointerUp(e, box, 'left')}
				onpointerenter={() => onDotEnter(box, 'left')}
				onpointerleave={() => onDotLeave(box, 'left')}
				oncontextmenu={(e) => { e.preventDefault(); e.stopPropagation(); removeConnectionsForDot(box.id, 'left'); }}
				style={labelsHidden && !usedDots.has(`${box.id}-left`) ? 'display:none' : ''}
			></span>
			<span
				class="dot dot-top"
				bind:this={dotEls[`${box.id}-top`]}
				onpointerdown={(e) => onDotPointerDown(e, box, 'top')}
				onpointerup={(e) => onDotPointerUp(e, box, 'top')}
				onpointerenter={() => onDotEnter(box, 'top')}
				onpointerleave={() => onDotLeave(box, 'top')}
				oncontextmenu={(e) => { e.preventDefault(); e.stopPropagation(); removeConnectionsForDot(box.id, 'top'); }}
				style={labelsHidden && !usedDots.has(`${box.id}-top`) ? 'display:none' : ''}
			></span>
			<span
				class="dot dot-right"
				bind:this={dotEls[`${box.id}-right`]}
				onpointerdown={(e) => onDotPointerDown(e, box, 'right')}
				onpointerup={(e) => onDotPointerUp(e, box, 'right')}
				onpointerenter={() => onDotEnter(box, 'right')}
				onpointerleave={() => onDotLeave(box, 'right')}
				oncontextmenu={(e) => { e.preventDefault(); e.stopPropagation(); removeConnectionsForDot(box.id, 'right'); }}
				style={labelsHidden && !usedDots.has(`${box.id}-right`) ? 'display:none' : ''}
			></span>
			<span
				class="dot dot-bottom"
				bind:this={dotEls[`${box.id}-bottom`]}
				onpointerdown={(e) => onDotPointerDown(e, box, 'bottom')}
				onpointerup={(e) => onDotPointerUp(e, box, 'bottom')}
				onpointerenter={() => onDotEnter(box, 'bottom')}
				onpointerleave={() => onDotLeave(box, 'bottom')}
				oncontextmenu={(e) => { e.preventDefault(); e.stopPropagation(); removeConnectionsForDot(box.id, 'bottom'); }}
				style={labelsHidden && !usedDots.has(`${box.id}-bottom`) ? 'display:none' : ''}
			></span>
			<span
				class="resize-handle"
				onpointerdown={(e) => onResizePointerDown(e, box)}
				onpointermove={(e) => onResizePointerMove(e, box)}
				onpointerup={() => onResizePointerUp(box)}
			></span>
		</div>
	{/each}
	{#if lassoActive}
		<div
			class="lasso"
			style="left: {Math.min(lassoStart.x, lassoEnd.x)}px; top: {Math.min(lassoStart.y, lassoEnd.y)}px; width: {Math.abs(lassoEnd.x - lassoStart.x)}px; height: {Math.abs(lassoEnd.y - lassoStart.y)}px;"
		></div>
	{/if}
</div>

<div class="actions-container" bind:clientWidth={actionsContainerWidth}>
	{#if connections.length > 0}
	<div class="actions-header">
		<button class="btn-list-actions" onclick={toggleActionsList}>
			{showActionsList ? 'Recargar Acciones' : 'Listar Acciones'}
		</button>
		{#if showActionsList}
			<button class="btn-set" onclick={clearLabels}>Set</button>
			<span class="run-wrap">
				<button class="btn-run" onclick={() => { if (animRunning) { animRunning = false; if (currentAnim) { currentAnim.cancel(); currentAnim = null; } animatingConnIdx = null; } else { runAllAnimations(); } }}>{animRunning ? 'Stop' : 'Run'}</button>
				<label class="loop-toggle" title="Loop animation">
					<input type="checkbox" bind:checked={loopAnimation} />
					<span class="loop-icon">♻</span>
				</label>
			</span>
		{/if}
	</div>
	{#if showActionsList}
		<div class="actions-panel" bind:this={actionsPanelEl}>
			{#if actionDisplay.length === 0}
				<p class="actions-empty">Sin conexiones</p>
			{:else}
				<table class="actions-table">
					<tbody>
					{#each actionDisplay as item, i}
						<tr
							class="action-row"
							class:dragging-row={draggingRowIdx === i}
							class:drag-over-row={dragOverRowIdx === i && draggingRowIdx !== i}
							draggable="true"
							ondragstart={(e) => onRowDragStart(e, i)}
							ondragover={(e) => onRowDragOver(e, i)}
							ondrop={(e) => onRowDrop(e, i)}
							ondragend={onRowDragEnd}
						>
							<td class="action-cell"><span class="action-num">{i + 1}</span></td>
							<td class="action-cell action-node action-from">{item.from}</td>
							<td class="action-cell">
								{#if item.label !== null}
									<span class="action-label">{item.label}</span>
								{:else}
									<span class="action-arrow">→</span>
								{/if}
							</td>
							<td class="action-cell action-node">{item.to}</td>
							<td class="action-cell"><button class="action-flip" onclick={() => flipRow(i)} title="Invertir">⇄</button></td>
						</tr>
					{/each}
					</tbody>
				</table>
			{/if}
		</div>
	{/if}
	{/if}
</div>

{#if confirmingBox}
<!-- svelte-ignore a11y_no_static_element_interactions -->
<div class="modal-overlay" onpointerdown={cancelRemoveBox}>
	<!-- svelte-ignore a11y_no_static_element_interactions -->
	<div class="modal" style="left: {confirmPos.x}px; top: {confirmPos.y}px;" onpointerdown={(e) => e.stopPropagation()}>
		<p>¿Eliminar nodo {confirmingBox.id}?</p>
		<div class="modal-actions">
			<button class="modal-btn cancel" onclick={cancelRemoveBox}>Cancelar</button>
			<button class="modal-btn confirm" onclick={confirmRemoveBox}>Eliminar</button>
		</div>
	</div>
</div>
{/if}

<style>
	:global(*, *::before, *::after) {
		font-family: 'Roboto', sans-serif;
		font-weight: 700;
	}

	.io-buttons {
		position: absolute;
		top: 1.2%;
		right: 1.5%;
		display: flex;
		gap: 8px;
		z-index: 20;
	}

	.toolbar {
		position: absolute;
		top: calc(5% + 5px);
		left: 1.5%;
		right: 1.5%;
		display: flex;
		gap: 8px;
		align-items: center;
		z-index: 20;
	}

	.toolbar-spacer {
		flex: 1;
	}

	.btn-io {
		padding: 6px 14px;
		background: rgba(255, 255, 255, 0.08);
		color: rgba(255, 255, 255, 0.7);
		border: 1px solid rgba(255, 255, 255, 0.2);
		border-radius: 6px;
		font-size: 12px;
		font-weight: 500;
		cursor: pointer;
		backdrop-filter: blur(8px);
		transition: background 0.2s, color 0.2s;
		white-space: nowrap;
	}

	.btn-io:hover {
		background: rgba(255, 255, 255, 0.18);
		color: white;
	}

	.create-buttons {
		position: fixed;
		top: 4%;
		left: 0.3%;
		display: flex;
		gap: 8px;
		z-index: 20;
	}

	.btn-create {
		padding: 6px 16px;
		background: rgba(255, 255, 255, 0.12);
		color: white;
		border: 1px solid rgba(255, 255, 255, 0.35);
		border-radius: 8px;
		font-size: 14px;
		font-weight: 500;
		cursor: pointer;
		backdrop-filter: blur(8px);
		transition: background 0.2s, border-color 0.2s;
	}

	.btn-create:hover {
		background: rgba(255, 255, 255, 0.22);
		border-color: rgba(255, 255, 255, 0.6);
	}

	.btn-create:active {
		background: rgba(255, 255, 255, 0.3);
	}

	.actions-container {
		position: fixed;
		top: 5%;
		right: 1%;
		width: fit-content;
		z-index: 25;
		display: flex;
		flex-direction: column;
		align-items: flex-end;
	}

	.actions-header {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	.btn-list-actions {
		padding: 8px 16px;
		background: #4a148c;
		color: white;
		border: 1px solid #7b1fa2;
		border-radius: 8px;
		font-size: 13px;
		font-weight: 500;
		cursor: pointer;
		transition: background 0.2s, border-color 0.2s;
	}

	.btn-list-actions:hover {
		background: #6a1b9a;
		border-color: #ab47bc;
	}

	.btn-list-actions:active {
		background: #38006b;
	}

	.btn-set,
	.btn-run {
		padding: 8px 16px;
		border-radius: 8px;
		font-size: 13px;
		font-weight: 700;
		cursor: pointer;
		transition: background 0.2s, border-color 0.2s;
	}

	.btn-set {
		background: #1565c0;
		color: white;
		border: 1px solid #1976d2;
	}

	.btn-set:hover {
		background: #1976d2;
		border-color: #42a5f5;
	}

	.btn-run {
		background: #1b5e20;
		color: white;
		border: 1px solid #2e7d32;
	}

	.btn-run:hover {
		background: #2e7d32;
		border-color: #66bb6a;
	}

	.run-wrap {
		position: relative;
		display: inline-flex;
	}

	.loop-toggle {
		position: absolute;
		top: -6px;
		right: -14px;
		display: inline-flex;
		align-items: center;
		cursor: pointer;
		gap: 1px;
	}

	.loop-toggle input {
		width: 14px;
		height: 14px;
		margin: 0;
		cursor: pointer;
		accent-color: #1b5e20;
	}

	.loop-icon {
		font-size: 16px;
		line-height: 1;
		color: #aaa;
	}

	.actions-panel {
		margin-top: 8px;
		width: max-content;
		background: white;
		border: 1px solid #ddd;
		border-radius: 10px;
		padding: 10px 12px;
		z-index: 15;
		pointer-events: auto;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		box-sizing: border-box;
	}

	.actions-table {
		border-collapse: collapse;
	}

	.action-row {
		cursor: grab;
		transition: background 0.15s;
	}

	.action-row:hover {
		background: #f5f5f5;
	}

	.action-row.dragging-row {
		opacity: 0.4;
		cursor: grabbing;
	}

	.action-row.drag-over-row {
		box-shadow: 0 -2px 0 0 #7b1fa2;
	}

	.action-row:last-child .action-cell {
		border-bottom: none;
	}

	.action-cell {
		text-align: left;
		vertical-align: middle;
		padding: 6px 14px;
		border-bottom: 1px solid #eee;
		white-space: nowrap;
		font-size: 12px;
	}

	.action-num {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		width: 22px;
		height: 22px;
		border-radius: 50%;
		border: 2px solid #1976d2;
		color: #1976d2;
		font-size: 11px;
		font-weight: 600;
	}

	.action-node {
		color: #000;
	}

	.action-from {
		text-align: left;
	}

	.action-label {
		background: #000;
		color: #fff;
		padding: 2px 8px;
		border-radius: 4px;
		font-size: 11px;
		white-space: nowrap;
	}

	.action-arrow {
		color: #999;
	}

	.action-flip {
		background: none;
		border: 1px solid #ddd;
		border-radius: 4px;
		color: #666;
		font-size: 14px;
		cursor: pointer;
		padding: 2px 6px;
		line-height: 1;
		transition: color 0.15s, border-color 0.15s;
	}

	.action-flip:hover {
		color: #1976d2;
		border-color: #1976d2;
	}

	.actions-empty {
		color: #666;
		font-size: 12px;
		text-align: center;
		margin: 8px 0;
	}

	.conn-label-wrap {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.conn-label {
		width: auto;
		max-width: 116px;
		height: 100%;
		background: rgba(0, 0, 0, 0.45);
		color: white;
		border: none;
		border-radius: 4px;
		text-align: center;
		font-size: 11px;
		outline: none;
		cursor: text;
		padding: 0 4px;
		box-sizing: border-box;
	}

	.btn-box {
		width: 20px;
		height: 20px;
		background: white;
		border-radius: 4px;
		position: relative;
		overflow: hidden;
	}

	.btn-dots {
		position: absolute;
		top: 2px;
		left: 2px;
		display: flex;
		gap: 2px;
	}

	.bd {
		width: 4px;
		height: 4px;
		border-radius: 50%;
	}

	.bd.r { background: #ff5f57; }
	.bd.y { background: #febc2e; }
	.bd.g { background: #28c840; }

	.btn-cylinder {
		width: 20px;
		height: 22px;
		position: relative;
	}

	.bc-top {
		position: absolute;
		top: 0;
		left: 0;
		width: 20px;
		height: 8px;
		background: linear-gradient(180deg, #e8f0fe, white);
		border-radius: 50%;
		z-index: 2;
	}

	.bc-body {
		position: absolute;
		top: 4px;
		left: 0;
		width: 20px;
		height: 14px;
		background: linear-gradient(180deg, white, #f0f4ff);
	}

	.bc-bottom {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 20px;
		height: 8px;
		background: #f0f4ff;
		border-radius: 50%;
		z-index: 1;
	}

	.workspace {
		position: absolute;
		top: 5%;
		left: 5%;
		width: 90%;
		height: 90%;
		border: 1px solid white;
		border-radius: 16px;
		transition: width 0.3s, left 0.3s;
	}

	.connections {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		pointer-events: none;
	}

	.connections foreignObject {
		pointer-events: auto;
	}

	.box {
		position: absolute;
		cursor: grab;
		user-select: none;
		touch-action: none;
	}

	.box.dragging {
		cursor: grabbing;
	}

	.box.selected {
		outline: 2px solid rgba(100, 149, 237, 0.8);
		outline-offset: 2px;
	}

	/* === PROJECT (Window Title Bar) === */
	.box.box-project {
		background: white;
		border-radius: 12px;
		box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
		overflow: hidden;
	}

	.box.box-project.dragging {
		box-shadow: 0 8px 30px rgba(0, 0, 0, 0.5);
	}

	.window-bar {
		width: 100%;
		height: 20px;
		background: #f0f0f0;
		border-radius: 12px 12px 0 0;
		display: flex;
		align-items: center;
		padding-left: 8px;
		gap: 4px;
	}

	.win-dot {
		width: 7px;
		height: 7px;
		border-radius: 50%;
	}

	.wd-r { background: #ff5f57; }
	.wd-y { background: #febc2e; }
	.wd-g { background: #28c840; }

	.box.box-project .label-project {
		top: 26px;
	}

	/* === DATABASE (Cilindro Tintado) === */
	.box.box-db {
		background: transparent;
		border-radius: 0;
		box-shadow: none;
		overflow: visible;
	}

	.cyl-top {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 28%;
		background: linear-gradient(180deg, #e8f0fe 0%, white 100%);
		border-radius: 50%;
		box-shadow: 0 2px 10px rgba(0,0,0,0.2);
		z-index: 2;
	}

	.cyl-body {
		position: absolute;
		top: 14%;
		left: 0;
		width: 100%;
		height: 72%;
		background: linear-gradient(180deg, white 0%, #f0f4ff 100%);
		box-shadow: 4px 0 20px rgba(0,0,0,0.15), -4px 0 20px rgba(0,0,0,0.15);
		overflow: hidden;
	}

	.label-db {
		top: 30%;
	}

	.cyl-bottom {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 28%;
		background: #f0f4ff;
		border-radius: 50%;
		box-shadow: 0 4px 20px rgba(0,0,0,0.3);
		z-index: 1;
	}

	.box.box-db .dot-top { top: -4px; z-index: 3; }
	.box.box-db .dot-bottom { bottom: -4px; z-index: 3; }
	.box.box-db .dot-left { top: 55%; }
	.box.box-db .dot-right { top: 55%; }

	.lasso {
		position: absolute;
		border: 1px solid rgba(100, 149, 237, 0.8);
		background: rgba(100, 149, 237, 0.12);
		pointer-events: none;
		z-index: 50;
	}

	.box-label {
		position: absolute;
		left: 10px;
		right: 10px;
		width: auto;
		background: transparent;
		border: none;
		border-bottom: 1px solid rgba(0, 0, 0, 0.2);
		text-align: center;
		font-size: 11px;
		color: #333;
		outline: none;
		cursor: text;
	}

	.box-label:first-of-type {
		top: 10px;
	}

	.resize-handle {
		position: absolute;
		bottom: 0;
		right: 0;
		width: 16px;
		height: 16px;
		cursor: nwse-resize;
		border-bottom-right-radius: 12px;
		background: linear-gradient(135deg, transparent 50%, rgba(0,0,0,0.15) 50%);
	}

	.box-label::placeholder {
		color: rgba(0, 0, 0, 0.3);
	}

	.dot {
		position: absolute;
		width: 12px;
		height: 12px;
		border-radius: 50%;
		cursor: crosshair;
		z-index: 10;
	}

	.dot.dot-left {
		left: -6px;
		top: 50%;
		margin-top: -6px;
	}

	.dot.dot-top {
		top: -6px;
		left: 50%;
		margin-left: -6px;
	}

	.dot.dot-right {
		right: -6px;
		top: 50%;
		margin-top: -6px;
	}

	.dot.dot-bottom {
		bottom: -6px;
		left: 50%;
		margin-left: -6px;
	}

	.dot {
		background: #6495ed;
		box-shadow: 0 0 6px rgba(100, 149, 237, 0.6);
	}

	.modal-overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: rgba(0, 0, 0, 0.3);
		z-index: 1000;
	}

	.modal {
		position: fixed;
		transform: translate(10px, 10px);
		background: rgba(30, 30, 30, 0.95);
		border: 1px solid rgba(255, 255, 255, 0.2);
		border-radius: 12px;
		width: 140px;
		height: 140px;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 14px;
		box-shadow: 0 16px 50px rgba(0, 0, 0, 0.6);
		backdrop-filter: blur(8px);
	}

	.modal p {
		color: white;
		font-size: 13px;
		margin: 0;
	}

	.modal-actions {
		display: flex;
		flex-direction: column;
		gap: 8px;
	}

	.modal-btn {
		padding: 5px 14px;
		border-radius: 8px;
		font-size: 12px;
		font-weight: 500;
		cursor: pointer;
		transition: background 0.2s, border-color 0.2s;
	}

	.modal-btn.cancel {
		background: rgba(255, 255, 255, 0.1);
		color: white;
		border: 1px solid rgba(255, 255, 255, 0.3);
	}

	.modal-btn.cancel:hover {
		background: rgba(255, 255, 255, 0.2);
	}

	.modal-btn.confirm {
		background: rgba(220, 60, 60, 0.8);
		color: white;
		border: 1px solid rgba(220, 60, 60, 0.6);
	}

	.modal-btn.confirm:hover {
		background: rgba(220, 60, 60, 1);
	}
</style>
