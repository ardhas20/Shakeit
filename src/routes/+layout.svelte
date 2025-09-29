<script>
  import { onMount, onDestroy } from "svelte";

  let diceFace = "⚀";
  let message = "Shake your device or press Roll";
  let lastShake = 0;
  let isShaking = false;

  const diceFaces = ["⚀", "⚁", "⚂", "⚃", "⚄", "⚅"];

  function rollDice() {
    if (isShaking) return;

    isShaking = true;

    let count = 0;
    const interval = setInterval(() => {
      diceFace = diceFaces[Math.floor(Math.random() * 6)];
      count++;
      if (count > 10) {
        clearInterval(interval);
        const roll = Math.floor(Math.random() * 6);
        diceFace = diceFaces[roll];
        message = `🎲 Result: ${roll + 1}`;
        isShaking = false;
      }
    }, 120);
  }

  function handleMotion(event) {
    const acc = event.accelerationIncludingGravity;
    if (!acc) return;

    const totalAcc = Math.sqrt(acc.x ** 2 + acc.y ** 2 + acc.z ** 2);
    const now = Date.now();

    if (totalAcc > 15 && now - lastShake > 1000) {
      rollDice();
      lastShake = now;
    }
  }

  onMount(() => {
    if (typeof window !== "undefined") {
      window.addEventListener("devicemotion", handleMotion);
    }
  });

  onDestroy(() => {
    if (typeof window !== "undefined") {
      window.removeEventListener("devicemotion", handleMotion);
    }
  });
</script>

<style>
  .neon-text {
    text-shadow: 0 0 10px #00eaff, 0 0 20px #00eaff, 0 0 40px #00eaff;
  }

  .dice {
    transition: transform 0.2s ease-in-out;
  }

  .shaking {
    animation: pulse 0.4s infinite;
  }

  @keyframes pulse {
    0% { transform: scale(1); filter: drop-shadow(0 0 5px #0ff); }
    50% { transform: scale(1.2); filter: drop-shadow(0 0 20px #0ff); }
    100% { transform: scale(1); filter: drop-shadow(0 0 5px #0ff); }
  }
</style>

<div class="min-h-screen flex flex-col items-center justify-center bg-black text-cyan-300 px-6">
  <h1 class="text-3xl md:text-4xl font-extrabold mb-10 neon-text text-center">
    Cyber Dice Roller
  </h1>

  <div class="mb-8 flex items-center justify-center">
    <div class={`text-9xl dice ${isShaking ? 'shaking' : ''}`}>
      {diceFace}
    </div>
  </div>

  <p class="text-xl font-medium mb-8 text-center text-gray-200">
    {message}
  </p>

  <button
    on:click={rollDice}
    class="px-8 py-3 rounded-full bg-cyan-400 text-black font-bold shadow-lg hover:bg-cyan-300 transition"
  >
    Roll
  </button>
</div>
