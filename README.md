// server.js
const express = require("express");
const bodyParser = require("body-parser");
const app = express();
const PORT = 3000;

// Dummy face data for comparison
const savedDescriptor = new Float32Array([/* pre-registered face descriptor data */]);

app.use(bodyParser.json({ limit: '5mb' }));
app.use(express.static("public"));

app.post("/api/verify-face", (req, res) => {
  const { descriptor } = req.body;

  if (!descriptor) {
    return res.json({ success: false });
  }

  // Compare with saved descriptor (basic cosine distance)
  const distance = cosineDistance(savedDescriptor, descriptor);

  if (distance < 0.6) {
    return res.json({ success: true, name: "John Doe" });
  } else {
    return res.json({ success: false });
  }
});

function cosineDistance(vec1, vec2) {
  let dot = 0.0;
  let normA = 0.0;
  let normB = 0.0;
  for (let i = 0; i < vec1.length; i++) {
    dot += vec1[i] * vec2[i];
    normA += vec1[i] * vec1[i];
    normB += vec2[i] * vec2[i];
  }
  return 1 - dot / (Math.sqrt(normA) * Math.sqrt(normB));
}

app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
