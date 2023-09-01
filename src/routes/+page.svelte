<script lang="ts">
  import { onMount } from "svelte";

  let recordingState: "inactive" | "recording" | "paused" = "inactive";
  let audio: HTMLAudioElement | null;
  let chunks: Blob[] = [];
  let mediaRecorder: MediaRecorder;

  const start = () => {
    mediaRecorder.start();
    recordingState = mediaRecorder.state;
  };

  const stop = () => {
    mediaRecorder.stop();
    recordingState = mediaRecorder.state;
    const blob = new Blob(chunks, { type: "audio/ogg; codecs=opus" });
    console.log(chunks);
    chunks = [];
    const audioURL = window.URL.createObjectURL(blob);
    if (audio) audio.src = audioURL;
  };

  onMount(() => {
    audio = document.querySelector("#audio");
    if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia)
      navigator.mediaDevices
        .getUserMedia({ audio: true, video: false })
        .then((stream) => {
          mediaRecorder = new MediaRecorder(stream);
          mediaRecorder.ondataavailable = (e) => {
            chunks.push(e.data);
          };
        })
        .catch((err) => {
          console.error(`The following getUserMedia error occurred: ${err}`);
        });
  });
</script>

<h1>AI-Avkodning</h1>
{#if recordingState === "inactive"}
  <button on:click={start}>Spela in</button>
{/if}
{#if recordingState === "recording"}
  <button on:click={stop}>Stäng av</button>
{/if}
<audio id="audio" controls src="" />

<style>
</style>
