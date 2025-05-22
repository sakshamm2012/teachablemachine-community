Learn more about how to use the code snippet on [github](https://github.com/googlecreativelab/teachablemachine-community/tree/master/libraries/image).

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Hand Gesture Recognition</title>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
  <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image"></script>
  <style>
    body {
      background: #d0f0ff;
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 40px;
    }
    h1 {
      color: #004080;
      font-weight: 900;
      margin-bottom: 30px;
    }
    #webcam-container {
      position: relative;
      width: 400px;
      height: 300px;
      border-radius: 20px;
      overflow: hidden;
      box-shadow: 0 0 20px #004080aa;
      margin-bottom: 20px;
    }
    video {
      transform: scaleX(-1);
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    #prediction-box {
      font-size: 26px;
      font-weight: bold;
      color: #004080;
      background: rgba(255, 255, 255, 0.7);
      padding: 15px 30px;
      border-radius: 12px;
      box-shadow: 0 5px 15px rgba(0, 64, 128, 0.3);
      width: 400px;
      text-align: center;
    }
  </style>
</head>
<body>

  <h1>Hand Gesture Recognition</h1>

  <div id="webcam-container">
    <video id="webcam" autoplay playsinline width="400" height="300"></video>
  </div>

  <div id="prediction-box">Loading model...</div>

  <script>
    const URL = "https://teachablemachine.withgoogle.com/models/zI5pBaBHr/";

    let model, webcam, maxPredictions;

    async function init() {
      const modelURL = URL + "model.json";
      const metadataURL = URL + "metadata.json";

      model = await tmImage.load(modelURL, metadataURL);
      maxPredictions = model.getTotalClasses();

      // Setup webcam
      webcam = new tmImage.Webcam(400, 300, true); // flip webcam
      await webcam.setup();
      await webcam.play();

      window.requestAnimationFrame(loop);

      document.getElementById("webcam-container").appendChild(webcam.canvas);
    }

    async function loop() {
      webcam.update();
      await predict();
      window.requestAnimationFrame(loop);
    }

    async function predict() {
      const prediction = await model.predict(webcam.canvas);
      prediction.sort((a, b) => b.probability - a.probability);
      const topPrediction = prediction[0];
      const label = topPrediction.className;
      const confidence = (topPrediction.probability * 100).toFixed(1);

      document.getElementById("prediction-box").innerText = `${label} (${confidence}%)`;
    }

    init();
  </script>

</body>
</html>
