<script>
  let arr = $state([]);
  let highlight_pivot = $state([]);
  let highlighted = $state([]);
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
    isSorted = false;
  });

  const partition = (arr, low, high, steps) => {
    let pivot = arr[high];
    steps.push({ type: "pivot", index: high });
    let i = low - 1;

    for (let j = low; j <= high - 1; j++) {
      steps.push({ type: "compare", indices: [j, high] });
      if (arr[j] < pivot) {
        i++;
        steps.push({ type: "swap", indices: [i, j] });
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
    }
    steps.push({ type: "swap", indices: [i + 1, high] });
    [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];

    return i + 1;
  };

  const animateSteps = async (steps) => {
    let currentArr = [...arr];
    for (let step of steps) {
      if (step.type === "pivot") {
        highlight_pivot = [step.index];
      } else if (step.type === "compare") {
        const [j, pivot] = step.indices;
        highlighted = [j, pivot];
      } else if (step.type === "swap") {
        const [i, j] = step.indices;
        [currentArr[i], currentArr[j]] = [currentArr[j], currentArr[i]];
        arr = [...currentArr];
      }
      await new Promise((resolve) => setTimeout(resolve, 50));
    }
    highlighted = [];
    highlight_pivot = [];
  };

  const quickSort = (arr, low, high, steps) => {
    if (low < high) {
      let pi = partition(arr, low, high, steps);

      quickSort(arr, low, pi - 1, steps);
      quickSort(arr, pi + 1, high, steps);
    }
    return steps;
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
      const newArr = [...arr];
      const steps = quickSort(newArr, 0, newArr.length - 1, []);
      await new Promise((r) => setTimeout(r, delay));
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
      <h1 class="text-2xl font-bold text-white">Quick Sort</h1>
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
            class:bg-green-500={highlighted.includes(index)}
            class:bg-red-500={highlight_pivot.includes(index)}
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
