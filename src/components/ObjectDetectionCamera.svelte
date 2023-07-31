<script lang='ts'>
  import { onMount, onDestroy } from "svelte";
  import { writable } from "svelte/store";
  import * as tf from "@tensorflow/tfjs";
  import * as cocoSsd from "@tensorflow-models/coco-ssd";

const emojiMap = {
  person: '👤',
  bicycle: '🚲',
  car: '🚗',
  motorcycle: '🏍️',
  airplane: '✈️',
  bus: '🚌',
  train: '🚆',
  truck: '🚚',
  boat: '⛵',
  'traffic light': '🚦',
  'fire hydrant': '🚒',
  'stop sign': '🛑',
  'parking meter': '🅿️',
  bench: '💺',
  bird: '🐦',
  cat: '🐱',
  dog: '🐶',
  horse: '🐴',
  sheep: '🐑',
  cow: '🐮',
  elephant: '🐘',
  bear: '🐻',
  zebra: '🦓',
  giraffe: '🦒',
  backpack: '🎒',
  umbrella: '☂️',
  handbag: '👜',
  tie: '👔',
  suitcase: '💼',
  frisbee: '🥏',
  skis: '🎿',
  snowboard: '🏂',
  'sports ball': '🏀',
  kite: '🪁',
  'baseball bat': '⚾',
  'baseball glove': '🧤',
  skateboard: '🛹',
  surfboard: '🏄',
  'tennis racket': '🎾',
  bottle: '🍾',
  'wine glass': '🍷',
  cup: '🍵',
  fork: '🍴',
  knife: '🍽️',
  spoon: '🥄',
  bowl: '🍜',
  banana: '🍌',
  apple: '🍎',
  sandwich: '🥪',
  orange: '🍊',
  broccoli: '🥦',
  carrot: '🥕',
  'hot dog': '🌭',
  pizza: '🍕',
  donut: '🍩',
  cake: '🍰',
  chair: '🪑',
  couch: '🛋️',
  'potted plant': '🪴',
  bed: '🛏️',
  'dining table': '🍽️',
  toilet: '🚽',
  tv: '📺',
  laptop: '💻',
  mouse: '🖱️',
  remote: '📱',
  keyboard: '⌨️',
  'cell phone': '📱',
  microwave: '🍚',
  oven: '🥘',
  toaster: '🍞',
  sink: '🚰',
  refrigerator: '🍕',
  book: '📚',
  clock: '⏰',
  vase: '🏺',
  scissors: '✂️',
  'teddy bear': '🧸',
  'hair drier': '💇',
  toothbrush: '🪥',
};



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
  
  onMount(async () => {
    try {
      await loadModel();
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
    } catch (err) {
      console.error("Error setting up the application:", err);
    }
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


<div class='flex justify-between'>
  <!-- Video container -->
  <div class='relative flex-1'>
    <!-- svelte-ignore a11y-media-has-caption -->
    <video 
        bind:this={video}
        class='w-full max-w-[640px] h-auto border border-gray-200 rounded-lg'
    ></video>
    <canvas
        bind:this={canvas}
        width="640"
        height="480"
        class='absolute top-0 left-0'
    ></canvas>
  </div>

  <!-- List container -->
  <div class='flex-1 border rounded-lg py-2'>
    <p class='text-gray-700 pl-3 text-2xl font-semibold border-b pb-2'>Objetos Detectados</p>
    <div class='' id='prediction-list'>
      {#each $predictionsStore as prediction, index}
        <div class='pl-3 py-2 even:bg-gray-100'>
          <p>{emojiMap[prediction.class]} {prediction.class}: {prediction.score.toFixed(2)}</p>
        </div>
      {/each}
    </div>
  </div>
</div>