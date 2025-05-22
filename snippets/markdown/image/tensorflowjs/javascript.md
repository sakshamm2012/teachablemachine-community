Learn more about how to use the code snippet on [github](https://github.com/googlecreativelab/teachablemachine-community/tree/master/libraries/image).

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Hand Gesture Recognition</title>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
  <style>
    body {
      background-color: #d0f0ff;
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 30px;
    }
    h1 {
      font-weight: 900;
      color: #004080;
      margin-bottom: 25px;
    }
    #webcam-container {
      margin: 0 auto;
      width: 320px;
      height: 320px;
      border-radius: 20px;
      overflow: hidden;
      box-shadow: 0 0 15px #004080aa;
    }
    #webcam-container canvas {
      width: 320px !important;
      height: 320px !important;
      transform: scaleX(-1);
    }
    #label-container {
      margin-top: 15px;
      font-size: 22px;
      font-weight: bold;
      color: #004080;
      min-height: 40px;
    }
    button {
      margin-top: 20px;
      padding: 12px 25px;
      font-size: 18px;
      font-weight: 700;
      color: white;
      background-color: #004080;
      border: none;
      border-radius: 12px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }
    button:hover {
      background-color: #0066cc;
    }
  </style>
</head>
<body>

  <h1>Hand Gesture Recognition</h1>

  <div id="webcam-container"></div>
  <div id="label-container">Click Start to begin</div>

  <button id="toggle-button" onclick="toggleWebcam()">Start</button>

  <script>
    const URL = "https://teachablemachine.withgoogle.com/models/y25i5boCF/";

    let model, webcam, maxPredictions;
    let isRunning = false;

    async function init() {
      const modelURL = URL + "model.json";
      const metadataURL = URL + "metadata.json";

      model = await tmImage.load(modelURL, metadataURL);
      maxPredictions = model.getTotalClasses();

      webcam = new tmImage.Webcam(320, 320, true);
      await webcam.setup();
      await webcam.play();

      document.getElementById("webcam-container").appendChild(webcam.canvas);

      isRunning = true;
      document.getElementById("toggle-button").innerText = "Stop";
      loop();
    }

    async function loop() {
      if (!isRunning) return;
      webcam.update();
      await predict();
      requestAnimationFrame(loop);
    }

    async function predict() {
      const predictions = await model.predict(webcam.canvas);
      predictions.sort((a, b) => b.probability - a.probability);
      const topPrediction = predictions[0];
      const label = topPrediction.className;
      const confidence = (topPrediction.probability * 100).toFixed(1);
      document.getElementById("label-container").innerText = `${label} (${confidence}%)`;
    }

    function toggleWebcam() {
      if (isRunning) {
        isRunning = false;
        webcam.stop();
        webcam.canvas.remove();
        document.getElementById("toggle-button").innerText = "Start";
        document.getElementById("label-container").innerText = "Webcam stopped";
      } else {
        init();
      }
    }
  </script>

</body>
</html>
