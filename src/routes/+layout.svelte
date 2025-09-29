<script>
  import { onMount } from "svelte";
 
  let diceValue = 1; // Würfelwert
  let lastShake = 0;
 
  // Funktion zum Würfeln
  function rollDice() {
    diceValue = Math.floor(Math.random() * 6) + 1;
    console.log("Dice rolled:", diceValue);
  }
 
  onMount(() => {
    function handleShake(event) {
      const acc = event.accelerationIncludingGravity;
      const totalAcc = Math.sqrt(
        acc.x * acc.x + acc.y * acc.y + acc.z * acc.z
      );
 
      const now = Date.now();
 
      // Wenn Schwellwert überschritten wird → Shake erkannt
      if (totalAcc > 15) {
        // Nur alle 1000ms
        if (now - lastShake > 1000) {
          rollDice();
          lastShake = now;
        }
      }
    }
 
    window.addEventListener("devicemotion", handleShake);
 
    return () => {
      window.removeEventListener("devicemotion", handleShake);
    };
  });
</script>
 
<main class="flex flex-col items-center justify-center h-screen bg-gradient-to-br from-purple-500 to-blue-500 text-white">
<h1 class="text-4xl font-bold mb-6">🎲 Shake Dice</h1>
 
  <!-- Anzeige Würfel -->
<div class="w-32 h-32 flex items-center justify-center text-6xl bg-white text-black rounded-2xl shadow-lg">
    {diceValue}
</div>
 
  <p class="mt-6 text-lg opacity-80">👉 Shake your phone to roll the dice!</p>
</main>
