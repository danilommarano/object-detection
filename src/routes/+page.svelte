<script lang="ts">
  import { onMount, onDestroy } from "svelte";
  import { writable } from "svelte/store";
  import * as tf from "@tensorflow/tfjs";
  import * as cocoSsd from "@tensorflow-models/coco-ssd";

  const predictionsStore = writable<cocoSsd.DetectedObject[]>([]);
  
    let video: HTMLVideoElement;
    let canvas: HTMLCanvasElement;
    let model: cocoSsd.ObjectDetection;
    let detecting = false;
  
    const colors: string[] = [
      "#FF0000",
      "#00FF00",
      "#0000FF",
      "#FFFF00",
      "#00FFFF",
      "#FF00FF",
    ];
  
    async function loadModel() {
      model = await cocoSsd.load();
    }
  
    async function detectObjects() {
      if (detecting) return;
      detecting = true;
  
      try {
        while (detecting) {
          if (video.readyState === video.HAVE_ENOUGH_DATA) {
            const newPredictions = await model.detect(video);
            predictionsStore.set(newPredictions);
  
            drawBoundingBoxes(newPredictions);
          }
          await new Promise((resolve) => requestAnimationFrame(resolve));
        }
      } catch (err) {
        console.error("Error detecting objects:", err);
      }
    }
  
    function drawBoundingBoxes(predictions: cocoSsd.DetectedObject[]) {
      const ctx = canvas.getContext("2d");
      if (ctx !== null) {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        predictions.forEach((prediction, index) => {
          const color = colors[index % colors.length];
          ctx.strokeStyle = color;
          ctx.lineWidth = 2;
          ctx.fillStyle = color;
          ctx.font = "16px Arial";

          ctx.beginPath();
          ctx.rect(
            prediction.bbox[0],
            prediction.bbox[1],
            prediction.bbox[2],
            prediction.bbox[3]
          );
          ctx.stroke();

          ctx.fillText(
            `${prediction.class} - ${prediction.score.toFixed(2)}`,
            prediction.bbox[0],
            prediction.bbox[1] > 10 ? prediction.bbox[1] - 5 : 10
          );
        });
      }

    }
  
    onMount(() => {
      loadModel();
      navigator.mediaDevices
        .getUserMedia({ video: true })
        .then((stream) => {
          video.srcObject = stream;
          video.play();
          detectObjects();
        })
        .catch((err) => {
          console.error("Error accessing the webcam:", err);
        });
    });
  
    onDestroy(() => {
    detecting = false;
    if (video && video.srcObject) {
      // @ts-ignore
      const tracks = video.srcObject.getTracks();
      tracks.forEach((track: { stop: () => any; }) => track.stop());
    }
  });
  </script>
  
  <style>
    video {
      width: 100%;
      max-width: 640px;
      height: auto;
      border: 1px solid #ccc;
      position: absolute;
      z-index: 1;
    }
  
    canvas {
      position: absolute;
      z-index: 2;
      top: 0;
      left: 0;
    }
  </style>
  
  <main>
    <!-- svelte-ignore a11y-media-has-caption -->
    <video bind:this={video}></video>
    <canvas
      bind:this={canvas}
      width="640"
      height="480"
      style="width: 100%; height: auto;"
    ></canvas>
    <div>
      {#if $predictionsStore.length > 0}
        <h2>Detected Objects:</h2>
        <ul>
          {#each $predictionsStore as prediction, index}
            <li>
              {prediction.class} - Confidence: {prediction.score.toFixed(2)}
            </li>
          {/each}
        </ul>
      {:else}
        <p>No objects detected.</p>
      {/if}
    </div>
  </main>
  