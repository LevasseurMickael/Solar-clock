<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'





let clockNumbersShown  //clock seen on screen


//Clock overall time function
function clockNumbers () {
  const allTime = new Date();
  let hourTime;
  let minuteTime;
  let secondTime;
  if (allTime.getHours() < 10) {
    hourTime = `0${allTime.getHours()}`;
  } else {
    hourTime = allTime.getHours();
  };
  if (allTime.getMinutes() < 10) {
    minuteTime = `0${allTime.getMinutes()}`;
  } else {
    minuteTime = allTime.getMinutes();
  };
  if (allTime.getSeconds() < 10) {
    secondTime = `0${allTime.getSeconds()}`;
  } else {
    secondTime = allTime.getSeconds();
  };
  clockNumbersShown = `${hourTime} : ${minuteTime} : ${secondTime}`;
  return clockNumbersShown;
}

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
