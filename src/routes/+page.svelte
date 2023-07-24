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

  <main class='flex justify-center text-gray-800'>

    <nav class='fixed flex justify-center border-b h-16 w-full'>
        <div class='container flex justify-between items-center h-full'>
            <p class='flex items-center font-extrabold text-2xl'><span class='text-xl'>♟</span>&nbspDanilo</p>
            <div>
                <div class='flex gap-8 text-lg font-semibold'>
                    <a class=''>Análises</a>
                    <a class=''>Sistemas</a>
                    <a class=''>Websites</a>
                    <a class=''>Modelos</a>
                    <a class=''>Blog</a>
                </div>
            </div>
        </div>
    </nav>

    <div class='w-full h-screen mt-16'>
        <div class='flex justify-center items-end h-48 bg-gradient-to-b from-white to-gray-100'>
            <div class='container text-gray-400'>
                <div class='flex gap-2 text-2xl'>
                    <div class='flex gap-2 items-center'>
                        <p class='font-semibold'>Machine Learning</p>
                    </div>
                    <p>/</p>
                    <div class='flex gap-2 items-center'>
                        <p class='font-semibold text-gray-700'>Object detection with COCO-SSD</p>
                    </div>

                </div>
            </div>

        </div>
        <div class='relative container w-[640px] h-[480px]'>
            <!-- svelte-ignore a11y-media-has-caption -->
            <video 
                bind:this={video}
                class='absolute w-full max-w-[640px] h-auto border border-gray-200 rounded-lg'
            ></video>
            <canvas
                bind:this={canvas}
                width="640"
                height="480"
                class='absolute top-0 left-0'
            ></canvas>
        </div>
    </div>
  </main>
  