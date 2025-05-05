<script lang="js">
  let arr = $state([]);
  let highlight_left = $state([]);
  let highlight_right = $state([]);
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

  function merge(left, right, start, steps) {
    const leftIndices = Array.from({ length: left.length }, (_, i) => start + i);
    const rightIndices = Array.from({ length: right.length }, (_, i) => start + left.length + i);
    steps.push({ type: "segment", leftIndices, rightIndices });
    const result = [];
    let i = 0,
      j = 0;
    while (i < left.length && j < right.length) {
      steps.push({ type: "compare", indices: [start + i, start + left.length + j] });
      if (left[i] < right[j]) {
        steps.push({ type: "overwrite", index: start + result.length, value: left[i] });
        result.push(left[i++]);
      } else {
        steps.push({ type: "overwrite", index: start + result.length, value: right[j] });
        result.push(right[j++]);
      }
    }
    while (i < left.length) {
      steps.push({ type: "overwrite", index: start + result.length, value: left[i] });
      result.push(left[i++]);
    }
    while (j < right.length) {
      steps.push({ type: "overwrite", index: start + result.length, value: right[j] });
      result.push(right[j++]);
    }
    return result;
  }

  function mergeSort(src, start, steps) {
    if (src.length <= 1) return src;
    const mid = Math.floor(src.length / 2);
    const left = mergeSort(src.slice(0, mid), start, steps);
    const right = mergeSort(src.slice(mid), start + mid, steps);
    return merge(left, right, start, steps);
  }

  async function animateSteps(steps) {
    highlight_left = [];
    highlight_right = [];
    for (const step of steps) {
      if (step.type === "segment") {
        highlight_left = [...step.leftIndices];
        highlight_right = [...step.rightIndices];
      }
      if (step.type === "compare") {
      } else if (step.type === "overwrite") {
        arr[step.index] = step.value;
        arr = [...arr];
      }
      await new Promise((r) => setTimeout(r, delay));
    }
    highlight_left = [];
    highlight_right = [];
  }

  const handleSort = async (e) => {
    e.preventDefault();
    if (isSorting) return;

    if (isSorted) {
      arr = Array.from({ length: 10 }, () => Math.floor(Math.random() * 9) + 1);
      isSorted = false;
    } else {
      isSorting = true;
      const copy = [...arr];
      const steps = [];
      mergeSort(copy, 0, steps);
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
      <h1 class="text-2xl font-bold text-white">Merge Sort</h1>
      <div class="mt-8 flex h-50 w-80 items-end border-2 bg-slate-300">
        {#each arr as v, i}
          <div
            class={`mt-8 h-32 w-10 border-1 border-black bg-red-700`}
            style:height={`${v * 10}px`}
          ></div>
        {/each}
      </div>
      <div class="grid grid-cols-10 mt-5 bg-gray-500">
        {#each arr as num, i}
          <div
            class="w-10 h-10 text-white flex items-center justify-center border-2 border-white"
            class:bg-blue-500={highlight_left.includes(i)}
            class:bg-red-500={highlight_right.includes(i)}
          >
            {num}
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
