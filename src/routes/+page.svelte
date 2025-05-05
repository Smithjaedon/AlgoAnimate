<script>
  const algorithms = {
    1: { name: "Bubble Sort", path: "/bubble" },
    2: { name: "Selection Sort", path: "/selection" },
    3: { name: "Insertion Sort", path: "/insertion" },
    4: { name: "Merge Sort", path: "/merge" },
    5: { name: "Quick Sort", path: "/quick" },
    6: { name: "Linear Search", path: "/linear" },
    7: { name: "Binary Search", path: "/binary" },
  };
  let path = "/";
  const randomizeAlgorithm = () => {
    const keys = Object.keys(algorithms);
    const randomKey = keys[Math.floor(Math.random() * keys.length)];
    path = algorithms[randomKey].path;
  };
</script>

<div class="hero bg-base-200 min-h-screen">
  <div class="hero-content text-center">
    <div class="max-w-md">
      <h1 class="text-5xl font-bold">
        <span class="text-blue-600">Algorithm</span> <span class="text-orange-500">Visualizer</span>
      </h1>
      <p class="py-6">
        Explore data structures and algorithms through interactive, intuitive, animated visuals.
        Dive in and experiment with your own inputs.
      </p>
      <button class="btn btn-primary" onclick={randomizeAlgorithm}
        ><a href={path}>Get Started</a></button
      >
    </div>
  </div>
</div>
