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
  dayTime: 86400,
  showClock: true,
  },
  {name: "Mars",
  dayTime: 88642.663 ,
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
let angles = $state({});

// Clock shown on user's page
let clockLocalUserTimeShown = $state();
let clockNumbersShown = $state();
let clockNumbersMarsShown = $state();
let planetRotation = $state();
let menuFieldset = $state({
  displayPlanets: "block",
  displaySettings: "none"
});
let settingsMemory = $state({
  timeChart: "24h-time",
});


// Pad used for all clock
function padForClock (){
  const padWith0 = n => n.toString().padStart(2, "0");
  return padWith0;
};

// Time chart change 24h 12h
function timeChartSwitch(planetHours) {
  if (settingsMemory.timeChart === "24h-time") {
  const timeChartChange = planetHours;
  return timeChartChange
  }
  // else {
  //   const timeChartChange = planetHours % 12;
  //   return timeChartChange
  // }
};

// Clock for Earth time in user time zone
function localTimeUser() {
  let currentTimeEartUtc = new Date();
  const localHourUser = currentTimeEartUtc.getHours();
  const localMinuteUser = currentTimeEartUtc.getMinutes();
  const localSecondUser = currentTimeEartUtc.getSeconds();
  clockLocalUserTimeShown = `${padForClock()(localHourUser)} : ${padForClock()(localMinuteUser)} : ${padForClock()(localSecondUser)}`;

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
    if(planet.name !== "Earth" && planet.name !== "Mars") {
    const planetTime = earthTimeUtcsecond * (86400 / planet.dayTime);
    const currentPlanetTime = planetTime % planet.dayTime;
    const hoursOnPlanet = Math.floor((currentPlanetTime % 86400) / 3600);
    const minutesOnPlanet = Math.floor((currentPlanetTime %3600) / 60);
    const secondOnPlanet = Math.floor(currentPlanetTime % 60);

  clocks[planet.name] = `${padForClock()(hoursOnPlanet)} : ${padForClock()(minutesOnPlanet)} : ${padForClock()(secondOnPlanet)}`;
    }
  })
  return clockNumbersShown
};


//Solar system rotation sync with time


function planetsRotation () {
  let currentTimeEartUtc = new Date();
  const earthTimeUtcsecond = (currentTimeEartUtc.getTime()) / 1000;
  allPlanetsOfSolarSystem.forEach((planet) => { 
    const planetTime = earthTimeUtcsecond * (86400 / planet.dayTime);
    const currentPlanetTime = planetTime % planet.dayTime;
    if (planet.name !== "Earth" && planet.name !== "Mars") {
      angles[planet.name] = (((currentPlanetTime / 43200) * 360) % 360);
    } else if (planet.name === "Earth") {
      const localHourUser = currentTimeEartUtc.getHours();
      const localMinuteUser = currentTimeEartUtc.getMinutes();
      const localSecondUser = currentTimeEartUtc.getSeconds();
      const localTimeUser = (localHourUser * 3600) + (localMinuteUser * 60) + (localSecondUser);
      angles[planet.name] = ((((localTimeUser)/ 43200) * 360) % 360);
    } else {
      const julianDate = getJulianDate();
      const marsSolDay = (julianDate - 2405522.0028779) / (1.0274912517);
      const marsTimeCoordinated = (marsSolDay % 1) *24;
      angles[planet.name] = ((((marsTimeCoordinated * (88642.663 / 24)) / 43200) * 360) % 360);  
    }
  });
  return planetRotation;
};

// Tabs selection handle
function handleOpenTabselected(tabSelected) {
  if(tabSelected === "planets") {
    menuFieldset.displayPlanets = "block";
    menuFieldset.displaySettings = "none";
  } else if (tabSelected === "settings") {
    menuFieldset.displayPlanets = "none";
    menuFieldset.displaySettings= "block";
  };
  return menuFieldset
};


// Time refresh set to 1s
clockNumbers();
localTimeUser();
clockMarsTime();
planetsRotation();

setInterval(clockNumbers, 1000);
setInterval(localTimeUser, 1000);
setInterval(clockMarsTime, 1000);
setInterval(planetsRotation, 1000);

// Localestorage to keep selected clock shown

function savingPlanet() {
localStorage.setItem("planetChoosed", JSON.stringify(allPlanetsOfSolarSystem.map((showClock) => showClock)));
};

function savingSettings() {
  localStorage.setItem("settings", JSON.stringify(settingsMemory))
  console.log(localStorage.getItem("settings"))
};

function savedPlanetChoose(){
  const planetSettingString = localStorage.getItem("planetChoosed");
  allPlanetsOfSolarSystem = JSON.parse(planetSettingString);
  const settingsSavedLocaly = localStorage.getItem("settings");
  settingsMemory = JSON.parse(settingsSavedLocaly);
  console.log(settingsMemory);
};

function loadingStorage() {
  if (localStorage.getItem("planetChoosed") !== null) {savedPlanetChoose()}
  // if (localStorage.getItem("settings") !== null) {savedPlanetChoose()}
}

onMount(loadingStorage)

</script>

<main class="clock-all-page">
  <!--  -->
<container class="clock-and-selector">
    <article class="planets-clock">
      {#each allPlanetsOfSolarSystem as planet}
        {#if planet.name === "Earth" && planet.showClock === true}
          <div class={planet.name}>
            <p>{planet.name}</p>
            {clockLocalUserTimeShown}
          </div>
        {:else if planet.name === "Mars" && planet.showClock === true}
          <div class={planet.name}>
            <p>{planet.name}</p>
            {clockNumbersMarsShown}
          </div>
          {:else if planet.showClock === true}
          <div class={planet.name}>
            <p>{planet.name}</p>
            {clocks[planet.name]}
          </div>
        {/if}
      {/each }
    </article>

    <article class="planets-list-area">
      <!-- Tabs menu -->
      <div class="menu-tabs">
        <button type="button" class="tab-links" id="planets-id" aria-label="planets tab" onclick={() => handleOpenTabselected("planets")}>Planets</button>
        <button type="button" class="tab-links" id="tab-id" aria-label="settings tab" onclick={() => handleOpenTabselected("settings")}>Settings</button>
      </div>

      <!-- Planets clocks to show menu -->
      <fieldset class= "planetsFieldset" style="display: {menuFieldset.displayPlanets}">
        <legend>Planet's clock</legend>
        <div class="planets-list">
        {#each allPlanetsOfSolarSystem as planet}
          <div class="${planet.name}" >
            <input type="checkbox" name="${planet.name}" bind:checked={planet.showClock} onchange={savingPlanet}/>
            <label for="${planet.name}">{planet.name}</label>
          </div>
        {/each}
        </div>
      </fieldset>


      <!-- Settings menu -->
      <fieldset class= "settingsFieldset" style="display: {menuFieldset.displaySettings}">
        <legend>Settings</legend>
        <div>
          <label for="clock-type">Clock convert</label>

          <select name="clock-convert" id="clock-type" bind:value={settingsMemory.timeChart} onchange= {savingSettings}>
            <option value="24h-time" selected>24h</option>
            <option value="12h-time">12h</option>
          </select>
        </div>
      </fieldset>
      
    </article>
  </container>


<!-- planets watch type clock area -->
  <container class="planets-clocks">
    <div class="Sun">
      <div class="sun-button-all">
        <div class="clockpic"></div>
        <button type="button" aria-label="sun button" class="sun-button"></button>
        <p class="sun-name">Sun</p>
      </div>
    
    {#each allPlanetsOfSolarSystem as planetTurning}
          <div class="all-planet-clock-{planetTurning.name}" style="transform: rotate({angles[planetTurning.name]}deg)">
            <div class="planet-and-text">
              <div class="planet-text" style="transform: rotate(-{angles[planetTurning.name]}deg)">
                <button type="button" aria-label="{planetTurning.name} button" class="{planetTurning.name}-clock" id={planetTurning.name}></button>
                <p class="{planetTurning.name}-name">{planetTurning.name}</p>
              </div>
            </div>
          </div>
        {/each}
    </div>
  </container>
</main>

<style>

main {
  background-image: url(./assets/a3aad14fdb499346d74950a51429026a.jpg);
  color: white;
  font-size: 1.3rem;
  line-height: 0.3rem;
  height: 100vh;
  width: 100vh;
}

.clock-and-selector {
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

.Mercury, .Mercury-name { color: #A9A9A9; }
.Venus, .Venus-name { color: #E6C229; }
.Earth, .Earth-name { color: #1DA1F2; }
.Mars, .Mars-name { color: #C1440E; }
.Jupiter, .Jupiter-name { color: #F7CC7F; }
.Saturn, .Saturn-name { color: #F5CBA7; }
.Uranus, .Uranus-name { color: #7FFFD4; }
.Neptune, .Neptune-name { color: #1E90FF; }

/* Menu selector */

.menu-tabs {
  display: flex;
}

.tab-links {
  font-size: 0.8rem;
  background-color: royalblue;
  margin: 0.3rem;
}


.planets-list-area {
  font-size: 1rem;
  line-height: 1rem;
}
.planets-list {
  display: flex;
  flex-direction: column;
  align-items: start;
}




/* Planets rotation area */

.planets-clocks {
  height: 75vh;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 0.75rem;
    background-repeat: no-repeat;
  background-size: cover;
  background-size: 80%;
  background-position-x: center;
  background-position-y: center;
  background-image: url(./assets/pngwing.com.png);
}

.Sun {
  display: flex;
  position: relative;
  transform: rotate(-90deg) translate(50px);
  align-items: center;
}

.sun-button-all{
  display: flex;
  flex-direction: column;
  justify-content: center;
  transform: rotate(90deg);
}


.sun-button {
  height: 60px;
  width: 60px;
  border-radius: 50%;
  background-color: #E6C229;
  padding: 0;
}

.sun-name {color: #E6C229;}

.planet-and-text {
  transform: rotate(90deg);

}

.all-planet-clock-Mercury {
  position: absolute;
  margin-left: 65px;
  transform-origin: -65px;
}
.all-planet-clock-Venus {
  position: absolute;
  margin-left: 100px;
  transform-origin: -100px;
}
.all-planet-clock-Earth {
  position: absolute;
  margin-left: 140px;
  transform-origin: -140px;
}
.all-planet-clock-Mars {
  position: absolute;
  margin-left: 170px;
  transform-origin: -170px;
}
.all-planet-clock-Jupiter {
  position: absolute;
  margin-left: 200px;
  transform-origin: -200px;
}
.all-planet-clock-Saturn {
  position: absolute;
  margin-left: 255px;
  transform-origin: -255px;
}
.all-planet-clock-Uranus {
  position: absolute;
  margin-left: 295px;
  transform-origin: -295px;
}
.all-planet-clock-Neptune {
  position: absolute;
  margin-left: 325px;
  transform-origin: -325px;
}





.Mercury-clock {
  background-color: #A9A9A9;
  padding: 0;
  height: 10px;
  width: 10px;
  border-radius: 50%;
}

.Venus-clock {
  background-color: #E6C229;
  padding: 0;
  height: 20px;
  width: 20px;
  border-radius: 50%;
}

.Earth-clock {
  background-color: #1DA1F2;
  padding: 0;
  height: 22px;
  width: 22px;
  border-radius: 50%;
}

.Mars-clock {
  background-color: #C1440E;
  padding: 0;
  height: 12px;
  width: 12px;
  border-radius: 50%;
}

.Jupiter-clock {
  background-color: #F7CC7F;
  padding: 0;
  height: 40px;
  width: 40px;
  border-radius: 50%;
}

.Saturn-clock {
  background-color: #F5CBA7;
  padding: 0;
  height: 35px;
  width: 35px;
  border-radius: 50%;
}

.Uranus-clock {
  background-color: #7FFFD4;
  padding: 0;
  height: 18px;
  width: 18px;
  border-radius: 50%;
}

.Neptune-clock {
  background-color: #1E90FF;
  padding: 0;
  height: 17px;
  width: 17px;
  border-radius: 50%;
}

</style>
