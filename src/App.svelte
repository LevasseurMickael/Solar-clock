<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'


// Clock shown on user's page
let clockLocalUserTimeShown
let clockNumbersShown
let clockNumbersMarsShown

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


// Clock for Mars time based on the Julian Date and Mars Sol Date

//Julian Date convertion
function getJulianDate (date = new Date()) {
  return (date.getTime() / 86400000) + 2440587.5;        // One Earth day = 86400000ms      Julian Date of the 1st January 1970 = 244587.5
};

//UTC to MTC convertion
function clockMarsTime () {
  const julianDate = getJulianDate();
  const marsSolDay = (julianDate - 2405522.0028779) / 1.0274912517;
  const marsTimeCoordinated = (marsSolDay % 1) *24;
  const hoursOnMars = Math.floor(marsTimeCoordinated);
  const minutesOnMars = Math.floor((marsTimeCoordinated % 1) * 60);
  const secondOnMars = Math.floor((((marsTimeCoordinated % 1 )*60) % 1) * 60);
  clockNumbersMarsShown = `${padForClock()(hoursOnMars)} : ${padForClock()(minutesOnMars)} : ${padForClock()(secondOnMars)}`;
  return clockNumbersMarsShown;
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
clockMarsTime();

setInterval(clockNumbers, 1000);
setInterval(localTimeUser, 1000);
setInterval(clockMarsTime, 1000);


</script>

<main>
  <p>{clockLocalUserTimeShown}</p>
  <p>{clockNumbersShown}</p>
  <p>{clockNumbersMarsShown}</p>
  
</main>

<style>
main {
  background-color: beige;
  color: black;
  font-size: 2rem;
}
</style>
