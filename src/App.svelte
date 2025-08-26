<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'





let clockNumbersShown  //clock seen on screen


//Clock Earth time
function clockNumbers () {
  const currentTimeEartUtc = new Date();
  const earthTimeUtcsecond = (currentTimeEartUtc.getTime()) / 1000;
  const planetTime = earthTimeUtcsecond * 1;  //TODO change *1 by planets ratio
  const currentPlanetTime = planetTime  //TODO change 1 by number of second per planet day
  const hoursOnPlanet = Math.floor((currentPlanetTime % 86400) / 3600);
  const minutesOnPlanet = Math.floor((currentPlanetTime %3600) / 60);
  const secondOnPlanet = Math.floor(currentPlanetTime % 60);
  const padWith0 = n => n.toString().padStart(2, "0");
  clockNumbersShown = `${padWith0(hoursOnPlanet)} : ${padWith0(minutesOnPlanet)} : ${padWith0(secondOnPlanet)}`;
  return clockNumbersShown;
}






// Time refresh set to 1s
clockNumbers();
setInterval(clockNumbers, 1000);


</script>

<main>
  {clockNumbersShown}
</main>

<style>
main {
  background-color: beige;
  color: black;
  font-size: 2rem;
}
</style>
