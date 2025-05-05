<script lang="js">
  import { onMount } from "svelte";
  let arr = $state([]);
  let highlighted = $state([]);
  let highlightedMain = $state([]);
  let highlightedFound = $state([]);
  let sliderVal = $state(2000);
  let delay = $state(100);
  let isSorting = $state(false);
  let isSorted = $state(false);

  const changeSpeed = (e) => {
    const target = e.target;
    const val = target.value ? parseInt(target.value) : 0;
    const max = 2000;
    const min = 200;
    const speed = max + min - val;
    sliderVal = val;
    delay = speed;
  };

  $effect(() => {
    const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 9) + 1);
    arr = [...initial];
  });

  const insertionSort = (arr) => {
    const steps = [];
    const newArr = [...arr];
    for (let i = 1; i < newArr.length; i++) {
      let key = newArr[i];
      steps.push({ type: "init", index: i, key });
      let j = i - 1;

      while (j >= 0 && newArr[j] > key) {
        newArr[j + 1] = newArr[j];
        steps.push({ type: "shift", from: j, to: j + 1 });
        j = j - 1;
      }
      newArr[j + 1] = key;
      steps.push({ type: "place", index: j + 1, key });
    }
    return steps;
  };

  const animateSteps = async (steps) => {
    let currentArr = [...arr];
    for (let step of steps) {
      if (step.type === "init") {
        highlightedMain = [step.index];
      } else if (step.type === "shift") {
        currentArr[step.to] = currentArr[step.from];
        highlighted = [step.from, step.to];
        arr = [...currentArr];
      } else if (step.type === "place") {
        currentArr[step.index] = step.key;
        arr = [...currentArr];
        highlightedFound = [step.index];
      }
      await new Promise((resolve) => setTimeout(resolve, delay));
    }
    highlighted = [];
    highlightedMain = [];
    highlightedFound = [];
  };

  const handleSort = async (e) => {
    e.preventDefault();
    if (isSorting) return;

    if (isSorted) {
      const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 9) + 1);
      arr = initial;
      isSorted = false;
    } else {
      isSorting = true;
      const steps = insertionSort([...arr]);
      await new Promise((r) => setTimeout(r, 50));
      await animateSteps(steps);
      isSorting = false;
      isSorted = true;
    }
  };
</script>

<div class="container mx-auto">
  <div class="mt-15 flex items-center justify-center">
    <div
      class="mt-4 flex h-120 w-150 flex-col items-center justify-center rounded-2xl bg-slate-600 p-4 shadow-lg"
    >
      <h1 class="text-2xl font-bold text-white">Insertion Sort</h1>
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
            class={`mt-10 flex h-10 w-10 items-center justify-center border-2 border-white bg-gray-500 shadow-2xl`}
            class:bg-blue-500={highlightedFound.includes(index)}
            class:bg-red-500={highlightedMain.includes(index)}
            class:bg-green-500={highlighted.includes(index)}
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
              >{isSorting ? "Sorting…" : isSorted ? "Reset" : "Start"}</button
            >
          </div>
        </form>
      </div>
    </div>
  </div>
</div>
