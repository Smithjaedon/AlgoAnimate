<script lang="ts">
	import { onMount } from 'svelte';
	let arr = $state<number[]>([]);
	let highlighted = $state<number[]>([]);
	let sliderVal = $state<number>(2000);
	let delay = $state<number>(100);
	let isSorting = $state<boolean>(false);
	let isSorted = $state<boolean>(false);

	let delayRef = 0;

	$effect(() => {
		if (delayRef !== delay) {
			delayRef = delay;
		}
		console.log(delayRef);
	});

	$effect(() => {
		const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 10) + 1);
		arr = [...initial];
		isSorted = false;
	});

	const changeSpeed = (e: Event) => {
		const target = e.target as HTMLInputElement;
		const val = target.value ? parseInt(target.value) : 0;
		const max = 2000;
		const min = 200;
		const speed = max + min - val;
		sliderVal = val;
		delay = speed;
	};
	const bubbleSort = (arr: number[]) => {
		const steps = [];
		const newArr = [...arr];
		for (let i = 0; i < newArr.length - 1; i++) {
			for (let j = 0; j < newArr.length - i - 1; j++) {
				steps.push({ type: 'compare', indices: [j, j + 1] });
				if (newArr[j] > newArr[j + 1]) {
					steps.push({ type: 'swap', indices: [j, j + 1] });
					[newArr[j], newArr[j + 1]] = [newArr[j + 1], newArr[j]];
				}
			}
		}
		return steps;
	};

	const handleSort = async (e: Event) => {
		e.preventDefault();
		if (isSorting) return;

		if (isSorted) {
			const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 10) + 1);
			arr = initial;
			isSorted = false;
		} else {
			isSorting = true;
			const steps = bubbleSort([...arr]);
			await new Promise((r) => setTimeout(r, 50));
			await animateSteps(steps);
			isSorting = false;
			isSorted = true;
		}
	};

	const animateSteps = async (steps: { type: string; indices: [number, number] }[]) => {
		let currentArr = [...arr];
		for (let step of steps) {
			const [i, j] = step.indices;
			highlighted = [i, j];
			$state.snapshot(highlighted);
			if (step.type === 'swap') {
				const temp = currentArr[i];
				currentArr[i] = currentArr[j];
				currentArr[j] = temp;
				arr = [...currentArr];
			}
			await new Promise((resolve) => setTimeout(resolve, delay));
			highlighted = [];
		}
	};
</script>

<div class="container mx-auto">
	<div class="mt-15 flex items-center justify-center">
		<div
			class="mt-4 flex h-120 w-150 flex-col items-center justify-center rounded-2xl bg-slate-600 p-4 shadow-lg"
		>
			<h1 class="text-2xl font-bold text-white">Bubble Sort</h1>
			<div class="mt-8 flex h-50 w-80 items-end border-2 bg-slate-300">
				{#each arr as v, i}
					<div
						class={`mt-8 h-32 w-10 border-1 border-black bg-red-700`}
						style:height={`${v * 10}px`}
					></div>
				{/each}
			</div>
			<div class="grid grid-cols-10">
				{#each arr as value, index}
					<div
						class={`mt-10 flex h-10 w-10 items-center justify-center border-2 border-white bg-gray-500 shadow-2xl ${
							highlighted.includes(index) ? 'bg-green-500' : ''
						}`}
					>
						{value}
					</div>
				{/each}
			</div>
			<div class="flex flex-col items-center justify-center">
				<form action="" class="mt-5 flex flex-col items-center justify-center">
					<div class="flex w-100 flex-col items-center justify-center gap-y-4">
						<input
							type="range"
							min={100}
							max="2000"
							step="10"
							value={sliderVal}
							class="range range-neutral"
							onchange={(e) => changeSpeed(e)}
						/>
						<button class="btn btn-wide" onclick={(e) => handleSort(e)} disabled={isSorting}
							>{isSorting ? 'Sorting…' : isSorted ? 'Reset' : 'Start'}</button
						>
					</div>
				</form>
			</div>
		</div>
	</div>
</div>
