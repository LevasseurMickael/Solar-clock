<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'
  import {onMount} from "svelte"



// Array of all planets

let allPlanetsOfSolarSystem = $state([
  {name: "Mercury",
  dayTime: 5068960,
  showClock: false
  },
  {name: "Venus",
  dayTime: 20995200,
  showClock: false
  },
  {name: "Earth",
  dayTime: 1,
  showClock: true
  },
  {name: "Mars",
  dayTime: 1,
  showClock: false
  },
  {name: "Jupiter",
  dayTime: 35640,
  showClock: false
  },
  {name: "Saturn",
  dayTime: 38520,
  showClock: false
  },
  {name: "Uranus",
  dayTime: 61920,
  showClock: false
  },
  {name: "Neptune",
  dayTime: 58000,
  showClock: false
  },
]);



let clocks = $state({});


// Clock shown on user's page
let clockLocalUserTimeShown = $state();
let clockNumbersShown = $state();
let clockNumbersMarsShown = $state();



// Pad used for all clock
function padForClock (){
  const padWith0 = n => n.toString().padStart(2, "0");
  return padWith0
};

// Clock for Earth time in user time zone
function localTimeUser() {
  let currentTimeEartUtc = new Date();
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
  const marsSolDay = (julianDate - 2405522.0028779) / (1.0274912517);
  const marsTimeCoordinated = (marsSolDay % 1) *24;
  const hoursOnMars = Math.floor(marsTimeCoordinated);
  const minutesOnMars = Math.floor((marsTimeCoordinated % 1) * 60);
  const secondOnMars = Math.floor((((marsTimeCoordinated % 1 )*60) % 1) * 60);
  clockNumbersMarsShown = `${padForClock()(hoursOnMars)} : ${padForClock()(minutesOnMars)} : ${padForClock()(secondOnMars)}`;
  return clockNumbersMarsShown;
};





//Clock solar system planets time convertion
function clockNumbers () {
  let currentTimeEartUtc = new Date();
  const earthTimeUtcsecond = (currentTimeEartUtc.getTime()) / 1000;

  allPlanetsOfSolarSystem.forEach((planet) => {
    const planetTime = earthTimeUtcsecond * (86400 / planet.dayTime);
    const currentPlanetTime = planetTime % planet.dayTime;
    const hoursOnPlanet = Math.floor((currentPlanetTime % 86400) / 3600);
    const minutesOnPlanet = Math.floor((currentPlanetTime %3600) / 60);
    const secondOnPlanet = Math.floor(currentPlanetTime % 60);
  clocks[planet.name] = `${padForClock()(hoursOnPlanet)} : ${padForClock()(minutesOnPlanet)} : ${padForClock()(secondOnPlanet)}`;
  })
  return clockNumbersShown;
};


// Time refresh set to 1s
clockNumbers();
localTimeUser();
clockMarsTime();

setInterval(clockNumbers, 1000);
setInterval(localTimeUser, 1000);
setInterval(clockMarsTime, 1000);


// Localestorage to keep selected clock shown

function savingPlanet() {
localStorage.setItem("planetChoosed", JSON.stringify(allPlanetsOfSolarSystem));
};

function savedPlanetChoose(){
  const planetSettingString = localStorage.getItem("planetChoosed");
  allPlanetsOfSolarSystem = JSON.parse(planetSettingString);
  return allPlanetsOfSolarSystem
};

onMount(savedPlanetChoose)

</script>

<main>
<container class="whole-page">

    <article class="planets-clock">
      {#each allPlanetsOfSolarSystem as planet}
        {#if planet.name === "Earth" && planet.showClock === true}
          <div class={planet.name}>
            <p id="${planet.name}">{planet.name}</p>
            {clockLocalUserTimeShown}
          </div>
        {:else if planet.name === "Mars" && planet.showClock === true}
          <div class={planet.name}>
            <p id="${planet.name}">{planet.name}</p>
            {clockNumbersMarsShown}
          </div>
          {:else if planet.showClock === true}
          <div class={planet.name}>
            <p id="${planet.name}">{planet.name}</p>
            {clocks[planet.name]}
          </div>
        {/if}
      {/each }
    </article>

    <article class="planets-list-area">
      <fieldset>
        <legend>Choose planet's clock</legend>
        <div class="planets-list">
        {#each allPlanetsOfSolarSystem as planet}
          <div class="${planet.name}" >
            <input type="checkbox" id="${planet.name}" name="${planet.name}" bind:checked={planet.showClock} onchange={savingPlanet}/>
            <label for="${planet.name}">{planet.name}</label>
          </div>
        {/each}
        </div>
      </fieldset>
    </article>
    
  </container>
</main>

<style>

main {
  background-color: black;
  color: white;
  font-size: 1.3rem;
  line-height: 0.3rem;
  height: 100vh;
  width: 100vh;
}

.whole-page {
  display: flex;
  height: 25vh;
  width: 100vh;
justify-content: space-between;
}

.planets-clock {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  justify-content: center;
}

.Mercury, .Venus, .Earth, .Mars, .Jupiter, .Saturn,.Uranus, .Neptune {
  padding: 0.7rem 2rem 0.7rem 2rem;
}

.Mercury { color: #A9A9A9; }
.Venus { color: #E6C229; }
.Earth { color: #1DA1F2; }
.Mars { color: #C1440E; }
.Jupiter { color: #F7CC7F; }
.Saturn { color: #F5CBA7; }
.Uranus { color: #7FFFD4; }
.Neptune { color: #1E90FF; }

.planets-list-area {
  font-size: 1rem;
  line-height: 1rem;
}
.planets-list {
  display: flex;
  flex-direction: column;
  align-items: start;
}
</style>
