let video = document.getElementById("video");
let message = document.getElementById("message");

Promise.all([
  faceapi.nets.tinyFaceDetector.loadFromUri("/models"),
  faceapi.nets.faceRecognitionNet.loadFromUri("/models"),
  faceapi.nets.faceLandmark68Net.loadFromUri("/models")
]).then(startVideo);

function startVideo() {
  navigator.mediaDevices.getUserMedia({ video: {} })
    .then(stream => (video.srcObject = stream))
    .catch(err => console.error(err));
}

async function markAttendance() {
  const detections = await faceapi
    .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
    .withFaceLandmarks()
    .withFaceDescriptors();

  if (detections.length === 0) {
    message.innerText = "No face detected. Please try again.";
    return;
  }

  const descriptor = detections[0].descriptor;
  // Replace this URL with your Google Apps Script Web App URL if using Google Sheets
  const response = await fetch("https://your-google-script-url", {
    method: "POST",
    body: JSON.stringify({ name: "John Doe", status: "Present" }),
  });

  if (response.ok) {
    message.innerText = "✅ Attendance marked for John Doe";
  } else {
    message.innerText = "❌ Failed to mark attendance.";
  }
}
