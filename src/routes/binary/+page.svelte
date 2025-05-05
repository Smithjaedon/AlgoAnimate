<script lang="js">
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

  $effect(() => {
    const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 100) + 1);
    const sorted = [...initial].sort((a, b) => a - b);
    arr = [...sorted];
    hasFound = false;
  });

  const binary = (arr, target) => {
    const steps = [];
    let left = 0;
    let right = arr.length - 1;
    let mid;

    while (left <= right) {
      mid = Math.floor((left + right) / 2);
      const leftIndices = Array.from({ length: mid - left + 1 }, (_, i) => left + i);
      const rightIndices = Array.from({ length: right - mid + 1 }, (_, i) => mid + i);
      steps.push({ type: "segment", leftIndices, rightIndices });
      if (arr[mid] === target) {
        steps.push({ type: "found", indices: [mid] });
        break;
      } else if (arr[mid] < target) {
        steps.push({ type: "compare", indices: [mid] });
        left = mid + 1;
      } else {
        steps.push({ type: "compare", indices: [mid] });
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
        highlight_left = [...step.leftIndices];
        highlight_right = [...step.rightIndices];
      } else if (step.type === "found") {
        highlight_found = [step.indices[0]];
      } else if (step.type === "compare") {
        highlight_selected = [step.indices[0]];
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

      const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 100) + 1);
      const sorted = [...initial].sort((a, b) => a - b);
      arr = sorted;
      hasFound = false;
      targetValue = "";
    } else {
      isSearching = true;
      const steps = binary(arr, targetValue);
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
            class={`mt-10 flex h-10 w-10 items-center justify-center border-2 border-white bg-gray-500 shadow-2xl`}
            class:bg-red-500={highlight_found.includes(index)}
            class:bg-blue-500={highlight_left.includes(index)}
            class:bg-orange-500={highlight_right.includes(index)}
            class:bg-green-500={highlight_selected.includes(index)}
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
