<script lang="js">
  let arr = $state([]);
  let highlight_selected = $state([]);
  let highlight_found = $state([]);
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
    arr = [...initial];
    hasFound = false;
  });

  const linear = (arr, target) => {
    const steps = [];
    for (let i = 0; i < arr.length; i++) {
      steps.push({ type: "compare", indices: [i] });
      if (arr[i] == target) {
        steps.push({ type: "found", indices: [i] });
        break;
      }
    }
    return steps;
  };

  const handleInput = (e) => {
    targetValue = e.target.value;
  };
  const animateSteps = async (steps) => {
    for (let step of steps) {
      if (step.type === "compare") {
        highlight_selected = [step.indices[0]];
      } else if (step.type === "found") {
        highlight_found = [step.indices[0]];
      }
      await new Promise((resolve) => setTimeout(resolve, delay));
    }
    highlight_selected = [];
  };

  const handleSort = async (e) => {
    e.preventDefault();
    if (isSearching) return;

    if (hasFound) {
      highlight_found = [];

      const initial = Array.from({ length: 10 }, () => Math.floor(Math.random() * 100) + 1);
      arr = initial;
      hasFound = false;
      targetValue = "";
    } else {
      isSearching = true;
      const steps = linear(arr, targetValue);
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
      <h1 class="text-2xl font-bold text-white">Linear Search</h1>
      <div class="grid grid-cols-10">
        {#each arr as value, index}
          <div
            class={`mt-10 flex h-10 w-10 items-center justify-center border-2 border-white bg-gray-500 shadow-2xl`}
            class:bg-green-500={highlight_selected.includes(index)}
            class:bg-red-500={highlight_found.includes(index)}
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
