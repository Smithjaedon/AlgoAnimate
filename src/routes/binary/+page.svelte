<script lang="js">
  import { get } from "svelte/store";

  let arr = $state([]);
  let highlight_selected = $state([]);
  let highlight_found = $state([]);
  let highlight_left = $state([]);
  let highlight_right = $state([]);
  let sliderVal = $state(2000);
  let delay = $state(100);
  let targetValue = $state("");
  let isSearching = $state(false);
  let hasFound = $state(false);

  const changeSpeed = (e) => {
    const target = e.target;
    const val = target.value ? parseInt(target.value) : 0;
    const max = 2000;
    const min = 200;
    const speed = max + min - val;
    sliderVal = val;
    delay = speed;
  };

  const getBgClass = (index) => {
    if (highlight_found.includes(index)) return "bg-red-500";
    if (highlight_left.includes(index)) return "bg-blue-500";
    if (highlight_right.includes(index)) return "bg-orange-500";
    if (highlight_selected.includes(index)) return "bg-green-500";
    return "bg-gray-500";
  };

  $effect(() => {
    const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 100) + 1);
    const sorted = [...initial].sort((a, b) => a - b);
    arr = [...sorted];
    hasFound = false;
  });

  const binary = (arr, target) => {
    const numericTarget = Number(target);
    const steps = [];
    let left = 0;
    let right = arr.length - 1;

    while (left <= right) {
      const mid = Math.floor((left + right) / 2);
      const leftIndices = Array.from({ length: mid - left }, (_, i) => left + i);
      const rightIndices = Array.from({ length: right - mid }, (_, i) => mid + 1 + i);
      steps.push({ type: "segment", leftIndices, rightIndices });
      steps.push({ type: "compare", indices: [mid] });
      if (arr[mid] === numericTarget) {
        steps.push({ type: "found", indices: [mid] });
        break;
      } else if (arr[mid] < numericTarget) {
        highlight_right = [...rightIndices];
        left = mid + 1;
      } else {
        highlight_left = [...leftIndices];
        right = mid - 1;
      }
    }
    return steps;
  };

  const handleInput = (e) => {
    targetValue = e.target.value;
  };
  const animateSteps = async (steps) => {
    for (let step of steps) {
      if (step.type === "segment") {
        highlight_selected = [];
        highlight_found = [];
        highlight_left = [...step.leftIndices];
        highlight_right = [...step.rightIndices];
      } else if (step.type === "found") {
        highlight_selected = [];
        highlight_left = [];
        highlight_right = [];
        highlight_found = [...step.indices];
      } else if (step.type === "compare") {
        highlight_selected = [...step.indices];
      }
      await new Promise((resolve) => setTimeout(resolve, delay));
    }
    highlight_selected = [];
    highlight_left = [];
    highlight_right = [];
  };

  const handleSort = async (e) => {
    e.preventDefault();
    if (isSearching) return;

    if (hasFound) {
      highlight_found = [];
      highlight_left = [];
      highlight_right = [];
      highlight_selected = [];

      const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 100) + 1);
      const sorted = [...initial].sort((a, b) => a - b);
      arr = sorted;
      hasFound = false;
      targetValue = "";
    } else {
      isSearching = true;
      const steps = binary(arr, Number(targetValue));
      await new Promise((r) => setTimeout(r, delay));
      await animateSteps(steps);
      isSearching = false;
      hasFound = true;
    }
  };
</script>

<div class="container mx-auto">
  <div class="mt-15 flex items-center justify-center">
    <div
      class="mt-4 flex h-120 w-150 flex-col items-center justify-around rounded-2xl bg-slate-600 p-4 shadow-lg"
    >
      <h1 class="text-2xl font-bold text-white">Binary Search</h1>
      <div class="grid grid-cols-10">
        {#each arr as value, index}
          <div
            class={`mt-10 flex h-10 w-10 items-center justify-center border-2 border-white bg-gray-500 shadow-2xl ${getBgClass(
              index
            )}`}
          >
            {value}
          </div>
        {/each}
      </div>
      <div class="flex flex-col items-center justify-center">
        <form action="" class="mt-5 flex flex-col items-center justify-center">
          <div class="flex w-100 flex-col items-center justify-center gap-y-4">
            <input
              type="text"
              placeholder="Enter number to search"
              value={targetValue}
              class="input"
              oninput={handleInput}
              disabled={isSearching}
            />
            <input
              type="range"
              min={100}
              max="2000"
              step="10"
              value={sliderVal}
              class="range range-neutral"
              onchange={(e) => changeSpeed(e)}
            />
            <button class="btn btn-wide" onclick={(e) => handleSort(e)} disabled={isSearching}
              >{isSearching ? "Searching…" : hasFound ? "Reset" : "Start"}</button
            >
          </div>
        </form>
      </div>
    </div>
  </div>
</div>
