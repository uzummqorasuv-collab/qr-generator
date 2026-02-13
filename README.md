<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Минимальный QR Генератор</title>
<style>
  body { font-family: sans-serif; text-align: center; margin-top: 50px; }
  input { width: 400px; padding: 8px; font-size: 16px; }
  button { padding: 8px 16px; font-size: 16px; margin: 10px; }
  #qr { display: none; margin: 0 auto; } /* Boshlang‘ichda rasm yashirin */
  p { font-size: 96px; margin: 15px 0 0 0; font-weight: bold; }
</style>
</head>
<body>
<h2>Минимальный генератор QR-кодов</h2>
<input id="text" type="text" placeholder="Введите текст">
<button id="generateBtn">Сгенерировать</button>
<button id="copyBtn">Копировать QR</button>
<br><br>

<img id="qr" src="" alt="QR-код появится здесь" />
<p id="qrText"></p>

<script>
const qrImg = document.getElementById('qr');
const qrText = document.getElementById('qrText');
const input = document.getElementById('text');

function generate() {
  const text = input.value;
  if(!text.trim()) return;
  const qr = 'https://api.qrserver.com/v1/create-qr-code/?size=360x360&data=' + encodeURIComponent(text);
  qrImg.src = qr;
  qrImg.style.display = 'block'; // rasmni ko‘rsatish
  qrText.textContent = text;
}

// Generate tugmasi
document.getElementById('generateBtn').addEventListener('click', generate);

// Enter bosilganda
input.addEventListener('keydown', e => {
  if(e.key === 'Enter') generate();
});

// Input bo‘sh bo‘lsa rasm va matn yo‘qoladi
input.addEventListener('input', () => {
  if(input.value.trim() === '') {
    qrImg.style.display = 'none';  // rasm yashirin
    qrText.textContent = '';
  }
});

// Copy tugmasi
document.getElementById('copyBtn').addEventListener('click', async () => {
  if(!qrImg.src || !qrText.textContent) return;

  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');

  canvas.width = 500;
  canvas.height = 600;

  // Oq fon
  ctx.fillStyle = '#ffffff';
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  const image = new Image();
  image.crossOrigin = "Anonymous";
  image.src = qrImg.src;
  image.onload = async () => {
    ctx.drawImage(image, 70, 20, 360, 360);

    ctx.fillStyle = '#000000';
    ctx.font = 'bold 96px sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText(qrText.textContent, canvas.width/2, 500);

    canvas.toBlob(async blob => {
      try {
        await navigator.clipboard.write([
          new ClipboardItem({'image/png': blob})
        ]);
        alert('QR-код и текст скопированы в буфер обмена!');
      } catch(e) {
        alert('Ошибка при копировании: ' + e);
      }
    });
  };
});
</script>
</body>
</html>
