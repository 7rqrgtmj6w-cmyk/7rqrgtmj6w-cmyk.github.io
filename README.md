<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>MyTV</title>
<style>
  body { margin:0; background:#000; color:#fff; font-family:-apple-system,sans-serif;
         padding:env(safe-area-inset-top) 12px 20px; }
  h1 { font-size:24px; }
  #player { width:100%; aspect-ratio:16/9; background:#111; border-radius:10px; overflow:hidden; }
  #player video, #player iframe { width:100%; height:100%; border:0; }
  input, button { font-size:16px; padding:10px; border-radius:8px; border:0; }
  input { width:100%; box-sizing:border-box; margin:6px 0; }
  button { background:#0a84ff; color:#fff; margin:4px 4px 4px 0; }
  .item { display:flex; justify-content:space-between; align-items:center;
          background:#1c1c1e; padding:10px; border-radius:8px; margin:6px 0; }
  .item span { flex:1; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
  .del { background:#ff453a; }
</style>
</head>
<body>
<h1>MyTV</h1>
<div id="player"></div>
<input id="name" placeholder="Name (optional)">
<input id="url" placeholder="Paste .m3u8, .mp4 or YouTube link">
<button onclick="playNow()">Play</button>
<button onclick="save()">Save</button>
<h3>Saved</h3>
<div id="list"></div>

<script>
const player = document.getElementById('player');
let saved = JSON.parse(localStorage.getItem('channels') || '[]');

function youtubeId(u) {
  const m = u.match(/(?:youtu\.be\/|v=|embed\/|shorts\/|live\/)([\w-]{11})/);
  return m ? m[1] : null;
}

function play(u) {
  const yt = youtubeId(u);
  if (yt) {
    player.innerHTML = `<iframe src="[youtube.com](https://www.youtube.com/embed/${yt}?playsinline=1&autoplay=1)"
      allow="autoplay; encrypted-media; picture-in-picture; fullscreen" allowfullscreen></iframe>`;
  } else {
    // iPhone Safari plays HLS (.m3u8) and MP4 natively
    player.innerHTML = `<video src="${u}" controls autoplay playsinline></video>`;
  }
}

function playNow() { const u = url.value.trim(); if (u) play(u); }

function save() {
  const u = url.value.trim(); if (!u) return;
  saved.push({ name: document.getElementById('name').value.trim() || u, url: u });
  localStorage.setItem('channels', JSON.stringify(saved));
  render();
}

function remove(i) { saved.splice(i,1); localStorage.setItem('channels', JSON.stringify(saved)); render(); }

function render() {
  list.innerHTML = saved.map((c,i) => `
    <div class="item"><span onclick="play(saved[${i}].url)">▶ ${c.name.replace(/</g,'&lt;')}</span>
    <button class="del" onclick="remove(${i})">✕</button></div>`).join('');
}
render();
</script>
</body>
</html>
