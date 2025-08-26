<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'



let clockLocalUserTimeShown
let clockNumbersShown

// Pad used for all clock
function padForClock (){
  const padWith0 = n => n.toString().padStart(2, "0");
  return padWith0
};

// Clock for Earth time in user time zone
function localTimeUser() {
  const currentTimeEartUtc = new Date();
  const localHourUser = currentTimeEartUtc.getHours();
  const localMinuteUser = currentTimeEartUtc.getMinutes();
  const localSecondUser = currentTimeEartUtc.getSeconds();
  clockLocalUserTimeShown = `${padForClock()(localHourUser)} : ${padForClock()(localMinuteUser)} : ${padForClock()(localSecondUser)}`;
  return clockLocalUserTimeShown
};





//Clock solar system planets time convertion
function clockNumbers () {
  const currentTimeEartUtc = new Date();
  const earthTimeUtcsecond = (currentTimeEartUtc.getTime()) / 1000;
  const planetTime = earthTimeUtcsecond * 1;  //TODO change *1 by planets ratio
  const currentPlanetTime = planetTime  //TODO change 1 by number of second per planet day
  const hoursOnPlanet = Math.floor((currentPlanetTime % 86400) / 3600);
  const minutesOnPlanet = Math.floor((currentPlanetTime %3600) / 60);
  const secondOnPlanet = Math.floor(currentPlanetTime % 60);
  clockNumbersShown = `${padForClock()(hoursOnPlanet)} : ${padForClock()(minutesOnPlanet)} : ${padForClock()(secondOnPlanet)}`;
  return clockNumbersShown;
}






// Time refresh set to 1s
clockNumbers();
localTimeUser();
setInterval(clockNumbers, 1000);
setInterval(localTimeUser, 1000);


</script>

<main>
  <p>{clockLocalUserTimeShown}</p>
  <p>{clockNumbersShown}</p>
  
</main>

<style>
main {
  background-color: beige;
  color: black;
  font-size: 2rem;
}
</style>
