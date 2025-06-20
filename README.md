const express = require("express");
const app = express();
const port = process.env.PORT || 3000;
let posts = [];

app.use(express.static("public"));
app.use(express.json());

app.get("/post", (req, res) => {
  res.json(posts);
});

app.post("/post", (req, res) => {
  const { nickname, message } = req.body;
  const time = new Date().toLocaleString();
  const id = Date.now();
  posts.push({ id, nickname, message, time, likes: 0, comments: [] });
  res.json(posts);
});

app.post("/like", (req, res) => {
  const id = req.body.id;
  posts = posts.map(p => p.id === id ? { ...p, likes: p.likes + 1 } : p);
  res.json(posts);
});

app.post("/comment", (req, res) => {
  const { id, comment } = req.body;
  posts = posts.map(p => p.id === id ? { ...p, comments: [...p.comments, comment] } : p);
  res.json(posts);
});

// 서버가 꺼지지 않도록 주기적으로 자기 자신 호출
setInterval(() => {
  fetch(`https://${process.env.PROJECT_DOMAIN}.glitch.me`).catch(() => {});
}, 280000);

app.listen(port, () => console.log(`Server running on port ${port}`));
