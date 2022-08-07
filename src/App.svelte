<script lang="ts">
  import Timer from "./lib/Timer.svelte";
  import { appWindow } from "@tauri-apps/api/window";
  appWindow.setResizable(false);

  addEventListener("keydown", async (e) => {
    if (e.metaKey && e.key == "m") {
      await appWindow.minimize();
    }
  });
</script>

<main>
  <Timer />
</main>

<style>
</style>
