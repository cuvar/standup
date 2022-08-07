<script lang="ts">
  let time = 0;
  let timerEnd = false;
  let timerRunning = false;
  let timerPaused = false;
  let intervalID;
  import { appWindow } from "@tauri-apps/api/window";

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
    timerRunning = true;
    intervalID = setInterval(runTimer, 950);
  }

  async function runTimer() {
    if (time <= 0 || timerEnd) {
      stopTimer();
      await showWindow();
    } else {
      time--;
    }
  }

  function stopTimer() {
    timerRunning = false;
    timerEnd = true;

    if (intervalID) {
      clearInterval(intervalID);
    }

    if (time <= 0) {
      setTimeout(() => {
        timerEnd = false;
      }, 2000);
    } else {
      timerEnd = false;
    }
    time = 0;
  }

  function pauseTimer() {
    timerPaused = true;
    if (intervalID) {
      clearInterval(intervalID);
    }
  }

  function resumeTimer() {
    timerPaused = false;
    intervalID = setInterval(runTimer, 1000);
  }

  async function showWindow() {
    await appWindow.show();
    await appWindow.setFocus();
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

<div class="flex flex-col items-center">
  {#if timerEnd}
    <h1>Timer ended!</h1>
  {:else}
    <h1>{secondsToTime(time)}</h1>
  {/if}

  {#if !timerEnd}
    <input
      type="time"
      id="time-input"
      on:keydown={onKeyDown}
      value="00:30:00"
    />
    <div class="mt-1">
      <button on:click={startTimer} class="text-green">Start</button>
      {#if timerPaused}
        <button on:click={resumeTimer} class="text-blue">Resume</button>
      {:else}
        <button on:click={pauseTimer} class="text-blue">Break</button>
      {/if}
      <!-- {#if timerRunning}
      {:else} -->
      <button on:click={stopTimer} class="text-red">Stop</button>
      <!-- {/if} -->
    </div>
  {/if}
</div>
