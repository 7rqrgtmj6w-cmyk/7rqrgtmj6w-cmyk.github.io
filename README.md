<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<title>RevSearch</title>
<style>
  body { margin:0; background:#000; color:#fff; font-family:-apple-system,sans-serif;
         padding:env(safe-area-inset-top) 14px 30px; }
  input[type=url] { width:100%; box-sizing:border-box; font-size:16px; padding:12px;
                    border-radius:10px; border:0; margin:8px 0; }
  .btn { display:block; width:100%; text-align:center; background:#0a84ff; color:#fff;
         padding:14px; border-radius:10px; font-size:17px; border:0; margin:8px 0;
         text-decoration:none; box-sizing:border-box; }
  .alt { background:#2c2c2e; }
  #preview { width:100%; max-height:40vh; object-fit:contain; border-radius:12px; display:none; }
  input[type=file] { display:none; }
  .note { color:#aaa; font-size:14px; }
</style>
</head>
<body>
<h1>RevSearch</h1>

<h3>From an image link</h3>
<input id="url" type="url" placeholder="Paste image URL (long-press image → Copy Link)">
<button class="btn" onclick="searchAll()">Search all engines</button>
<div id="links"></div>

<h3>From your photos</h3>
<label class="btn alt">📷 Choose photo<input id="file" type="file" accept="image/*"></label>
<img id="preview">
<button class="btn" id="shareBtn" style="display:none" onclick="sharePhoto()">Send to Google Lens / other app</button>
<p class="note">Choose the <b>Google</b> app in the share sheet to run Lens on the photo.
Bing and TinEye also take uploads on their sites, using the buttons below.</p>
<a class="btn alt" href="[bing.com](https://www.bing.com/visualsearch)" target="_blank">Bing Visual Search</a>
<a class="btn alt" href="[tineye.com](https://tineye.com)" target="_blank">TinEye</a>
<a class="btn alt" href="[yandex.com](https://yandex.com/images/)" target="_blank">Yandex Images</a>

<script>
const engines = {
  "Google Lens": u => `[lens.google.com](https://lens.google.com/uploadbyurl?url=${u})`,
  "Bing":        u => `[bing.com](https://www.bing.com/images/search?view=detailv2&iss=sbi&q=imgurl:${u})`,
  "Yandex":      u => `[yandex.com](https://yandex.com/images/search?rpt=imageview&url=${u})`,
  "TinEye":      u => `[tineye.com](https://tineye.com/search?url=${u})`
};

function searchAll() {
  const raw = url.value.trim(); if (!raw) return;
  const u = encodeURIComponent(raw);
  // iOS blocks multiple pop-ups, so show one tap-able button per engine
  links.innerHTML = Object.entries(engines).map(([name, f]) =>
    `<a class="btn alt" href="${f(u)}" target="_blank">Open in ${name}</a>`).join("");
}

let chosen;
file.onchange = () => {
  chosen = file.files[0]; if (!chosen) return;
  preview.src = URL.createObjectURL(chosen);
  preview.style.display = "block";
  shareBtn.style.display = "block";
};

async function sharePhoto() {
  if (navigator.canShare && navigator.canShare({ files: [chosen] })) {
    await navigator.share({ files: [chosen] });
  } else {
    alert("Sharing not supported here — use the engine buttons below to upload.");
  }
}
</script>
</body>
</html>
