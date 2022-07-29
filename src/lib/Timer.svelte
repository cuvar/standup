<script lang="ts">
  let time = 0;
  let timerEnd = false;
  let intervalID;

  function getTime() {
    const timer = document.getElementById("time-input");
    // @ts-ignore
    if (timer.value == "") {
      return -1; // throw error
    }

    // @ts-ignore
    const timeArray = timer.value.split(":");
    const hours = timeArray[0];
    const minutes = timeArray[1];
    time = minutes * 60 + hours * 3600;
    return time;
  }

  function startTimer() {
    stopTimer();
    timerEnd = false;

    time = getTime();
    if (time <= 0) {
      time = 0;
      return;
    }

    intervalID = setInterval(() => {
      if (time <= 0 || timerEnd) {
        stopTimer();
      } else {
        time--;
      }
    }, 1000);
  }

  function stopTimer() {
    time = 0;
    if (intervalID) {
      clearInterval(intervalID);
    }
    timerEnd = true;
    setTimeout(() => {
      timerEnd = false;
    }, 2000);
  }

  function onKeyDown(e: KeyboardEvent) {
    if (e.key == "Enter") {
      startTimer();
    }
  }

  function secondsToTime(secs: number) {
    const hours = Math.floor(secs / 3600);
    const minutes = Math.floor((secs % 3600) / 60);
    const seconds = secs % 60;

    if (hours == 0) {
      return `${pad(String(minutes), 2)}:${pad(String(seconds), 2)}`;
    }
    return `${pad(String(hours), 2)}:${pad(String(minutes), 2)}:${pad(
      String(seconds),
      2
    )}`;
  }

  function pad(str, num) {
    if (str.length < num) {
      return pad("0" + str, num);
    }
    return str;
  }
</script>

{#if timerEnd}
  <h1>Timer ended</h1>
{:else}
  <h1>{secondsToTime(time)}</h1>
{/if}

<div class="flex flex-col w-lg">
  {#if !timerEnd}
    <input type="time" id="time-input" on:keydown={onKeyDown} />
    <button on:click={startTimer} class="mt-1">Start</button>
  {/if}
</div>
