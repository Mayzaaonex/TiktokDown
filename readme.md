# TiktokDown

TikTok video downloader **without watermark** — scrape from [ssstik.io](https://ssstik.io/), upload automatically to Uguu.se, output **pure JSON**.

![Node.js](https://img.shields.io/badge/node-%3E%3D18-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![GitHub stars](https://img.shields.io/github/stars/Mayzaaonex/TiktokDown?style=social)

---

## ✨ Features

- ✅ Download TikTok **without watermark** (SD)
- ✅ Download **audio MP3** separately
- ✅ Get **thumbnail** and **author avatar**
- ✅ Auto-upload to **Uguu.se** (public URL)
- ✅ **Parallel** download + upload (video & audio simultaneously)
- ✅ Output **pure JSON** — easy to parse
- ✅ **Zero dependency** — only requires Node.js 18+
- ✅ Works on **PC** and **VPS**

---

## 📦 Installation

### Requirement

- **Node.js >= 18** (uses built-in `fetch`, `FormData`, `Blob`)

Check Node version:
```bash
node -v
```

If not installed, download from [nodejs.org](https://nodejs.org/).

### Clone Repository

```bash
git clone https://github.com/Mayzaaonex/TiktokDown.git
cd TiktokDown
```

No `npm install` needed — zero dependency.

---

## 🚀 Usage

### Basic

```bash
node sstik.js "https://vt.tiktok.com/ZSqECbYqS/"
```

### Supported TikTok URL Formats

- `https://vt.tiktok.com/XXXXX/` (short URL)
- `https://vm.tiktok.com/XXXXX/`
- `https://www.tiktok.com/@username/video/1234567890`
- `https://m.tiktok.com/v/1234567890.html`

---

## 🌐 Base URL

This scraper uses **[ssstik.io](https://ssstik.io/)** as the base URL for scraping TikTok videos.

**Base URL:** `https://ssstik.io`

The scraped video/audio files are temporarily hosted on:
- **CDN:** `https://tikcdn.io/ssstik/` (media hosting)
- **Uploader:** `https://uguu.se/upload.php` (public file hosting)

---

## 📤 Example Output

### Success

```json
{
  "creator": "Mayzaa",
  "status": true,
  "result": {
    "url": "https://vt.tiktok.com/ZSqECbYqS/",
    "author": {
      "name": "A.",
      "avatar": "https://tikcdn.io/ssstik/a/aHR0cHM6Ly9wMTkt..."
    },
    "thumbnail": "https://tikcdn.io/ssstik/p/aHR0cHM6Ly9wMTYt...",
    "video": "https://h.uguu.se/vnBbhSMU.mp4",
    "audio": "https://d.uguu.se/NykFAlul.mp3",
    "stats": {
      "likes": "546.5K",
      "comments": "1.3K",
      "shares": "48.6K"
    },
    "size": {
      "video": 1361686,
      "audio": 93876
    }
  }
}
```

### Error

```json
{
  "creator": "Mayzaa",
  "status": false,
  "message": "Token tt not found"
}
```

### Field Description

| Field | Type | Description |
|-------|------|-------------|
| `creator` | string | Script author name |
| `status` | boolean | `true` if success, `false` if failed |
| `result.url` | string | Original TikTok URL input |
| `result.author.name` | string | TikTok author name |
| `result.author.avatar` | string | Author avatar URL |
| `result.thumbnail` | string | Video thumbnail URL |
| `result.video` | string | MP4 video URL (Uguu) |
| `result.audio` | string | MP3 audio URL (Uguu) |
| `result.stats.likes` | string | Like count |
| `result.stats.comments` | string | Comment count |
| `result.stats.shares` | string | Share count |
| `result.size.video` | number | Video size (bytes) |
| `result.size.audio` | number | Audio size (bytes) |
| `message` | string | Error message (if `status: false`) |

---

## 🔧 How It Works

```
1. Fetch session token from ssstik.io
2. Submit TikTok URL, get HTML result
3. Parse HTML  → extract video URL, audio URL, metadata
4. Download video & audio → into Buffer (parallel)
5. Upload to Uguu.se → get public URL
6. Output JSON → to stdout
```

---

## ⚙️ Integration with Other Apps

### Node.js

```javascript
const { execSync } = require("child_process");

const output = execSync('node sstik.js "https://vt.tiktok.com/ZSqECbYqS/"').toString();
const data = JSON.parse(output);

console.log(data.result.video); // video URL
console.log(data.result.audio); // audio URL
```

### Express.js (as API)

```javascript
const express = require("express");
const { execSync } = require("child_process");
const app = express();

app.get("/api/tiktok", (req, res) => {
  const url = req.query.url;
  if (!url) return res.status(400).json({ error: "URL required" });

  try {
    const output = execSync(`node sstik.js "${url}"`, { timeout: 60000 }).toString();
    res.type("application/json").send(output);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(3000, () => console.log("Server running at http://localhost:3000"));
```

Call: `http://localhost:3000/api/tiktok?url=https://vt.tiktok.com/ZSqECbYqS/`

---

## ⚠️ Important Notes

1. **Uguu file expiry 3 hours** — files are auto-deleted by Uguu after 3 hours. For permanent storage, re-download and save yourself.
2. **Rate limit** — do not spam. Add 3-5 second delay between requests.
3. **Slow connection** — total time ~7-15s depending on internet speed and server. Upload speed matters.
4. **HD video** — This script only fetches **SD (without watermark)**. For HD, ssstik.io requires a "reward ad" that cannot be bypassed.
5. **Disclaimer** — Use only for content you have rights to or that is permitted. Respect TikTok creator copyright.

---

## 📝 License

MIT License

Copyright (c) 2026 Mayzaa

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

See [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Mayzaa**
- GitHub: [@Mayzaaonex](https://github.com/Mayzaaonex)
- Repository: [TiktokDown](https://github.com/Mayzaaonex/TiktokDown)

---

## 🙏 Credits

- [ssstik.io](https://ssstik.io/) — TikTok scraper backend (base URL)
- [Uguu.se](https://uguu.se/) — Temporary file hosting
- [tikcdn.io](https://tikcdn.io/) — Media CDN

---

## ⭐ Star History

If this project is useful, give it a ⭐!

[![Star History Chart](https://api.star-history.com/svg?repos=Mayzaaonex/TiktokDown&type=Date)](https://star-history.com/#Mayzaaonex/TiktokDown&Date)
