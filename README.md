const express = require("express");
const app = express();
const PORT = 3000;

// ----------------------
// 1️⃣ Logging Middleware
// ----------------------
app.use((req, res, next) => {
  const currentTime = new Date().toLocaleString();
  console.log(`[${currentTime}] ${req.method} ${req.url}`);
  next(); // move to the next middleware or route
});

// ----------------------
// 2️⃣ Authentication Middleware
// ----------------------
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers["authorization"];

  if (!authHeader) {
    return res.status(401).json({
      message: "Authorization header missing or incorrect",
    });
  }

  const token = authHeader.split(" ")[1]; // extract token after 'Bearer'

  if (token === "mysecrettoken") {
    next(); // valid token → proceed
  } else {
    return res.status(403).json({
      message: "Invalid token. Access denied.",
    });
  }
};

// ----------------------
// 3️⃣ Public Route
// ----------------------
app.get("/", (req, res) => {
  res.send("This is a public route. No authentication required.");
});

// ----------------------
// 4️⃣ Protected Route
// ----------------------
app.get("/protected", authenticateToken, (req, res) => {
  res.send("You have accessed a protected route with a valid Bearer token!");
});

// ----------------------
// 5️⃣ Start Server
// ----------------------
app.listen(PORT, () => {
  console.log(`✅ Server running on http://localhost:${PORT}`);
});
