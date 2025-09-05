<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'
  import {onMount} from 'svelte'
  import { preventDefault } from 'svelte/legacy';



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


//  All planet name, nickname and description for the pop-up

const allPlanetDescription = [
  { name: "Sun",
  nickname: "The Ultimate Drama Queen",
  description1: "Description: A giant ball of plasma, the Sun makes up 99.8% of the solar system's mass. It's a nuclear fusion powerhouse, burning 600 million tons of hydrogen every second.",
  description2: "Funny Anecdote: The Sun is so powerful that if you could harness just one second of its energy, you could power Earth for 500,000 years. Yet, it still can’t keep your Wi-Fi from dropping. Priorities, Sun. Priorities.",
  link: "https://en.wikipedia.org/wiki/Sun",
  },
  { name: "Mercury",
nickname: "The Speedy Hothead",
  description1: "Description: The smallest and closest planet to the Sun, Mercury zips around its orbit in just 88 Earth days. It's a world of extremes: scorching days (430°C) and freezing nights (-180°C).",
  description2: "Funny Anecdote: If you lived on Mercury, you'd celebrate your birthday every 3 Earth months—but you'd also age faster than anyone else in the solar system. Talk about a midlife crisis!",
  link: "https://en.wikipedia.org/wiki/Mercury_(planet)",
  },
    { name: "Venus",
  nickname: "The Toxic Twin",
  description1: "Description: Similar in size to Earth, Venus is wrapped in thick, toxic clouds of sulfuric acid. Its surface is hot enough to melt lead (475°C), and the atmospheric pressure would crush you like a soda can.",
  description2: "Funny Anecdote: Venus rotates so slowly that a day there (243 Earth days) is longer than its year (225 Earth days). So if you moved there, you'd arrive after your own birthday.",
  link: "https://en.wikipedia.org/wiki/Venus",
  },
    { name: "Earth",
  nickname: "The Oddball Oasis",
  description1: "Description: The only known planet with life, liquid water, and a breathable atmosphere. Also, the only planet not named after a god (unless you count Gaia, but that's more of a nickname).",
  description2: "Funny Anecdote: Earth is the only place where you can order pizza. Coincidence? Probably not.",
  link: "https://en.wikipedia.org/wiki/Earth",
  },
    { name: "Mars",
  nickname: "The Rusty Desert",
  description1: "Description: Known as the Red Planet due to iron oxide (rust) on its surface, Mars is home to the solar system's tallest volcano (Olympus Mons) and deepest canyon (Valles Marineris).",
  description2: "Funny Anecdote: Mars has two moons, Phobos and Deimos, which translate to “Fear” and “Panic.” Perfect names for a planet that's probably haunted by failed rovers.",
  link: "https://en.wikipedia.org/wiki/Mars",
  },
    { name: "Jupiter",
  nickname: "The Gas Giant with a Temper",
  description1: "Description: The largest planet, Jupiter is a stormy behemoth with a Great Red Spot—a hurricane that's been raging for at least 400 years.",
  description2: "Funny Anecdote: Jupiter is so massive that it could fit all the other planets inside it—twice. It's basically the solar system's overprotective big brother.",
  link: "https://en.wikipedia.org/wiki/Jupiter",
  },
    { name: "Saturn",
  nickname: "The Bling King",
  description1: "Description: Famous for its dazzling rings made of ice and rock, Saturn is a gas giant with 83 moons (and counting).",
  description2: "Funny Anecdote: Saturn's rings are mostly chunks of ice, some as small as dust and others as big as mountains. If you tried to ice skate on them, you'd fall forever.",
  link: "https://en.wikipedia.org/wiki/Saturn",
  },
    { name: "Uranus",
  nickname: "The Sideways Jokester",
  description1: "Description: Uranus rotates on its side (98° tilt), likely due to a ancient cosmic collision. It's also the coldest planet, with temperatures dropping to -224°C.",
  description2: "Funny Anecdote: Yes, the jokes write themselves. But did you know Uranus smells like rotten eggs? (Thanks, hydrogen sulfide!)",
  link: "https://en.wikipedia.org/wiki/Uranus",
  },
    { name: "Neptune",
  nickname: "The Windy Mystery",
  description1: "Description: The farthest planet from the Sun, Neptune is a blue ice giant with the strongest winds in the solar system (2,100 km/h).",
  description2: "Funny Anecdote: Neptune was predicted by math before it was seen through a telescope. It's like the solar system's version of a plot twist.",
  link: "https://en.wikipedia.org/wiki/Neptune",
  }
];

// Month for the date shown
const monthForYear = [
  "January", "February", "March", "April", "May", "June",
  "July", "August", "September", "October", "Nomvember", "December"
]



let clocks = $state({});
let angles = $state({});
let openPopUp = $state(false);
let buttonPressedName = $state();



// Clock shown on user's page
let clockLocalUserTimeShown = $state();
let CreatedClockTimeShown = $state({});
let todayDate = $state()
let clockNumbersMarsShown = $state();
let planetRotation = $state();
let menuFieldset = $state({
  displayPlanets: "block",
  displaySettings: "none"
});
let settingsMemory = $state({
  timeChart: "",
});

let createdUserClock = $state([])

let createdUserClockName = $state("");
let createUserClockMP = $state();
let createUserClockTz = $state();



// Pad used for all clock
function padForClock (){
  const padWith0 = n => n.toString().padStart(2, "0");
  return padWith0;
};

// Time chart change 24h 12h
function timeChartSwitch(planetHours) {
  if (settingsMemory.timeChart === "24h time") {
  const timeChartChange = planetHours;
  return timeChartChange
  }
  else {
    const timeChartChange = planetHours % 12;
    return timeChartChange
  }
};


// am and pm appear

function amPmSetting(planetHours) {
  if (settingsMemory.timeChart === "12h time") {
    if (planetHours < 12) {
      const amPmTime = "am"
      return amPmTime
    }
    else {
      const amPmTime = "pm"
      return amPmTime
    };
  } else {
    const amPmTime = ""
    return amPmTime
  }
  };



// Clock for Earth time in user time zone
function localTimeUser() {
  let currentTimeEartUtc = new Date();
  const localYearUser = currentTimeEartUtc.getFullYear();
  const localMonthUser = currentTimeEartUtc.getMonth();
  const localDayUser = currentTimeEartUtc.getDate();
  const localHourUser = currentTimeEartUtc.getHours();
  const localMinuteUser = currentTimeEartUtc.getMinutes();
  const localSecondUser = currentTimeEartUtc.getSeconds();
  clockLocalUserTimeShown = `${padForClock()(timeChartSwitch(localHourUser))} : ${padForClock()(localMinuteUser)} : ${padForClock()(localSecondUser)} ${amPmSetting(localHourUser)}`;
  todayDate = `${monthForYear[localMonthUser]} ${padForClock()(localDayUser)} ${padForClock()(localYearUser)}`;
};

// Clock creation by user

let createdUserClockArray = {};

function handleCreateUserClock (event) {
  event.preventDefault();
  createdUserClockArray = {
  nameClock: createdUserClockName,
  timeZoneMP: createUserClockMP,
  timeZoneChart: createUserClockTz
  };
  createdUserClock.push(createdUserClockArray);
  createdUserClockName = "";
  createUserClockMP = "";
  createUserClockTz = "";
  savingSettings();
};

  function createdClorkShwon () {
  let currentCreatedEartUtc = new Date();
  createdUserClock.forEach((namedClock) => {
  let newClockTimeUtcsecond = (currentCreatedEartUtc.getTime()) / 1000;
  const hoursOnNewClock = Math.floor(((newClockTimeUtcsecond + (namedClock.timeZoneMP * namedClock.timeZoneChart * 3600)) % 86400) / 3600);
  const minutesOnNewClock = Math.floor((newClockTimeUtcsecond %3600) / 60);
  const secondOnNewClock = Math.floor(newClockTimeUtcsecond % 60);
  CreatedClockTimeShown[namedClock.nameClock] = `${padForClock()(timeChartSwitch(hoursOnNewClock))} : ${padForClock()(minutesOnNewClock)} : ${padForClock()(secondOnNewClock)} ${amPmSetting(hoursOnNewClock)}`;
  })
};

function handleDeleteClock(clockName) {
  const deletedClockCreated = createdUserClock.filter(function(clocksCheck) {
    return clocksCheck.nameClock !== clockName;
  });
  createdUserClock = deletedClockCreated
  savingSettings();
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
  clockNumbersMarsShown = `${padForClock()(timeChartSwitch(hoursOnMars))} : ${padForClock()(minutesOnMars)} : ${padForClock()(secondOnMars)} ${amPmSetting(hoursOnMars)}`;
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
  clocks[planet.name] = `${padForClock()(timeChartSwitch(hoursOnPlanet))} : ${padForClock()(minutesOnPlanet)} : ${padForClock()(secondOnPlanet)} ${amPmSetting(hoursOnPlanet)}`;
    }
  })
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

// Open the description pop-up

function showPlanetInformation(buttonName) {
  buttonPressedName = buttonName;
  openPopUp = true;
  
};

// Close the description pop-up
function closePlanetInformation() {
  openPopUp = false;
};

function closePlanetInformationOut(event) {
  if (event.target.classList.contains("planet-description")) {
    closePlanetInformation();
  }
};

function keyDownPopUp (event) {
  if (event.key === "Enter" || event.key === " " || event.ket === "Esc") {
    closePlanetInformation();
  }
}

function handleModalClick(event) {
    event.stopPropagation();
  }


// Time refresh set to 1s
clockNumbers();
localTimeUser();
clockMarsTime();
planetsRotation();
createdClorkShwon();

setInterval(clockNumbers, 1000);
setInterval(localTimeUser, 1000);
setInterval(clockMarsTime, 1000);
setInterval(planetsRotation, 1000);
setInterval(createdClorkShwon, 1000);

// Localestorage to keep selected clock shown


function savingSettings() {
  localStorage.setItem("settings", JSON.stringify(settingsMemory.timeChart));
  localStorage.setItem("clock-created", JSON.stringify(createdUserClock));
};

function loadingLocalSettings() {
  if (localStorage.getItem("settings") !== null) {savedSettingsChoosed()}
  else {settingsMemory.timeChart = "24h time";
  };
};

function checkingLocalClockCreated () {
  if (localStorage.getItem("clock-created") !== null) {savedCreatedClock()}
  else {createdUserClock = []}
};

function savedCreatedClock (){
  const localClockCreatedsString = localStorage.getItem("clock-created");
  createdUserClock = (JSON.parse(localClockCreatedsString));
};

function savedSettingsChoosed(){
  const localSettingsString = localStorage.getItem("settings");
  settingsMemory.timeChart = JSON.parse(localSettingsString);

};

// LocalStorage planets

function savingPlanet() {
localStorage.setItem("planetChoosed", JSON.stringify(allPlanetsOfSolarSystem.map((showClock) => showClock)));
};
function loadingStorage() {
  if (localStorage.getItem("planetChoosed") !== null) {savedPlanetChoose()};
  
}

function savedPlanetChoose(){
  const planetSettingString = localStorage.getItem("planetChoosed");
  allPlanetsOfSolarSystem = JSON.parse(planetSettingString);
};

function allOnMountFunction() {
  loadingStorage();
  loadingLocalSettings()
  checkingLocalClockCreated ()
};
onMount(allOnMountFunction);



</script>

<main class="clock-all-page">
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
      {#each createdUserClock as createdClock}
        <div class={createdClock.nameClock}>
            <p>{createdClock.nameClock}</p>
            {CreatedClockTimeShown[createdClock.nameClock]}
          </div>
      {/each}
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
        <p class="today-date">{todayDate}</p>
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
        <fieldset class= "settingsFieldset" style="display: {menuFieldset.displaySettings}" >
          <legend>Settings</legend>
          <div class="all-settings-window">
            <div class="time-chart-choice">
              <label for="clock-type" >Clock convert</label>
              <select name="clock-convert" id="clock-type" bind:value={settingsMemory.timeChart} onchange={savingSettings}>
                <option  value="24h time">24h time</option>
                <option  value="12h time">12h time</option>
              </select>
            </div>
              <form onsubmit={handleCreateUserClock}>
                <div class="create-clock">
                  <p>Create a clock</p>
                  <input type="text" id="new-clock-name" required class="input-name" placeholder="Clock name" bind:value={createdUserClockName}>
                  <label for="select-plus-minus">Time zone</label>
                  <div class="select-time-zone">
                    <select name="Time zone range" id="select-plus-minus" bind:value={createUserClockMP}>
                      <option value="1">+</option>
                      <option value="-1">-</option>
                    </select>
                
                      <select name="Time zone" id="select-time-zone" bind:value={createUserClockTz}>
                        <option value="0">0</option>
                        <option value="1">1</option>
                        <option value="2">2</option>
                        <option value="3">3</option>
                        <option value="4">4</option>
                        <option value="5">5</option>
                        <option value="6">6</option>
                        <option value="7">7</option>
                        <option value="8">8</option>
                        <option value="9">9</option>
                        <option value="10">10</option>
                        <option value="11">11</option>
                        <option value="12">12</option>
                      </select>
                
                  </div>
                  <button type="submit" aria-label="submit new clock" class="submit-button">Create</button>
                  <div class="clock-created-list">
                    {#each createdUserClock as clock}
                      <p>{clock.nameClock}</p>
                      <button type="button" name={clock.nameClock} aria-label="delete clock created" onclick={() => handleDeleteClock(clock.nameClock)}>X</button>
                    {/each}
                  </div>
                </div>
              </form>
          </div>
        </fieldset>
    </article>
  </container>


<!-- planets watch type clock area -->
  <container class="planets-clocks">
    <div class="Sun">
      <div class="sun-button-all">
        <div class="clockpic"></div>
        <button type="button" aria-label="sun button" class="sun-button" onclick={() => showPlanetInformation("Sun")}></button>
        <p class="sun-name">Sun</p>
      </div>

      <!-- Description of planet when planet clicked -->
      {#if openPopUp}

        <div class="planet-description" role="button" onclick={closePlanetInformationOut} onkeydown={keyDownPopUp} tabindex="0">
            <button onclick={handleModalClick} aria-label="place which do not close"></button>
            <div class="inside-planet-description" role="document">
              <div class="pop-up-button-container"><button type="button" class="pop-up-button" aria-label="close the pop-up" onclick={closePlanetInformation}>X</button></div>
              <div class="pop-up-overlay">
                {#each allPlanetDescription as planetDescription}
                  {#if planetDescription.name === buttonPressedName}
                  <p style="color: var(--{planetDescription.name})">{planetDescription.name}, {planetDescription.nickname}</p>
                  <ul class="pop-up-text">
                    <li>{planetDescription.description1}</li>
                    <li>{planetDescription.description2}</li>
                  </ul>
                  <a href={planetDescription.link} aria-label="link to wikipedia for ${planetDescription.name}">More information on Wikipedia</a>
                  {/if}
                {/each}
              </div>
            </div>
          </div>
      {/if}

      {#each allPlanetsOfSolarSystem as planetTurning}
        <div class="all-planet-clock-{planetTurning.name}" style="transform: rotate({angles[planetTurning.name]}deg)">
          <div class="planet-and-text">
            <div class="planet-text" style="transform: rotate(-{angles[planetTurning.name]}deg)">
              <button type="button" aria-label="{planetTurning.name} button" class="{planetTurning.name}-clock" id={planetTurning.name} onclick={() => showPlanetInformation(planetTurning.name)}></button>
              <p class="{planetTurning.name}-name">{planetTurning.name}</p>
            </div>
          </div>
        </div>
      {/each}
    </div>
  </container>
</main>

<style>

:root {
  --Sun: #E6C229;
  --Mercury: #A9A9A9;
  --Venus: #E6C229; 
  --Earth: #1DA1F2; 
  --Mars: #C1440E; 
  --Jupiter: #F7CC7F; 
  --Saturn: #F5CBA7; 
  --Uranus: #7FFFD4; 
  --Neptune: #1E90FF; 
}


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
  padding: 0.7rem 0.8rem 0.7rem 1rem;
}

.Mercury, .Mercury-name { color: var(--Mercury); }
.Venus, .Venus-name { color: var(--Venus); }
.Earth, .Earth-name { color: var(--Earth); }
.Mars, .Mars-name { color: var(--Mars); }
.Jupiter, .Jupiter-name { color: var(--Jupiter); }
.Saturn, .Saturn-name { color: var(--Saturn); }
.Uranus, .Uranus-name { color: var(--Uranus); }
.Neptune, .Neptune-name { color: var(--Neptune); }

/* Menu selector */

.menu-tabs {
  display: flex;
}

.tab-links {
  font-size: 0.6rem;
  background-color: royalblue;
  margin: 0.3rem;
  padding: 5px 20px 5px 20px;
}


.planets-list-area {
  font-size: 1rem;
  line-height: 1rem;
  width: 20vh;
}
.planets-list {
  display: flex;
  flex-direction: column;
  align-items: start;
}

.today-date {
  margin: 5px 0 7px 0 ;
  color: var(--Sun);
  background-color: rgba(0, 0, 0, 0.8);
}


.all-settings-window {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-size: 0.8rem;
}


.time-chart-choice {
  display: flex;
  flex-direction: column;
}

.create-clock {
  padding: 0.5rem;
}

.input-name {
  width: 80%;
  padding: 3px;
  margin: 0 0.5rem 0.5rem 0.5rem;
}

.select-time-zone {
display: flex;
flex-direction: row;
justify-content: space-between;
margin: 0.7rem 0 0.7rem 0;
width: 80%;
}

#select-plus-minus, #select-time-zone {
  margin-left: 1rem;
}

.submit-button {
  background-color: rgba(3, 8, 63, 0.836);
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

.planet-description {
  width: 100vh;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.inside-planet-description {
  position: fixed;
  transform: rotate(90deg);
  width: 75vh;
  height: 35vh;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 500;
}

.pop-up-button-container {
  display: flex;
  height: 100%;
  width: 100%;
  justify-content: end;
}

.pop-up-button {
  position: relative;
  align-self: flex-start;
  background-color: darkred;
  color: white;
  z-index: 1000;
}


.pop-up-overlay {
    position: absolute;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: 1rem;
    line-height: 1.5rem;
  }

.pop-up-text {
  margin: 1rem;
  padding: 1rem;
  column-gap: 0.5rem;
}

.Sun {
  display: flex;
  position: relative;
  transform: rotate(-90deg) translate(50px);
  align-items: center;
  justify-content: center;
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
  background-color: var(--Sun);
  padding: 0;
}

.sun-name {color: var(--Sun);}

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
