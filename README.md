<!-- index.html --><!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Face Attendance System</title>
  <script defer src="https://cdn.jsdelivr.net/npm/face-api.js"></script>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { text-align: center; padding: 2rem; }
    video { border: 2px solid #333; border-radius: 10px; }
    .status { margin-top: 1rem; font-weight: bold; }
  </style>
</head>
<body>
  <h2>Employee Face Attendance</h2>
  <video id="video" width="480" height="360" autoplay muted></video>
  <br>
  <button class="btn btn-primary mt-3" onclick="markAttendance()">Mark Attendance</button>
  <div class="status" id="status"></div>  <script>
    const video = document.getElementById('video');

    Promise.all([
      faceapi.nets.tinyFaceDetector.loadFromUri('/models'),
      faceapi.nets.faceLandmark68Net.loadFromUri('/models'),
      faceapi.nets.faceRecognitionNet.loadFromUri('/models')
    ]).then(startVideo);

    function startVideo() {
      navigator.mediaDevices.getUserMedia({ video: {} })
        .then(stream => video.srcObject = stream)
        .catch(err => console.error("Camera error:", err));
    }

    async function markAttendance() {
      const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
        .withFaceLandmarks().withFaceDescriptors();

      if (detections.length === 0) {
        document.getElementById('status').innerText = 'No face detected!';
        return;
      }

      // Placeholder: Replace this with real face matching logic
      const employeeName = "John Doe";
      const time = new Date().toLocaleString();

      fetch('http://localhost:3000/attendance', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: employeeName, time })
      })
      .then(res => res.json())
      .then(data => {
        document.getElementById('status').innerText = 'Attendance marked for ' + employeeName;
      })
      .catch(err => {
        console.error(err);
       
