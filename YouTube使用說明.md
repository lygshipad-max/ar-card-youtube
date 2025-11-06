# WebAR YouTube 播放器 - 使用指南 📺

## 🎯 主要改動說明

### 原本（本地影片）
```html
<a-video 
    src="#video" 
    width="1.6" 
    height="0.9">
</a-video>

<a-assets>
    <video id="video" src="./video.mp4"></video>
</a-assets>
```

### 現在（YouTube）
```html
<!-- 使用 aframe-html-shader 組件 -->
<a-plane
    material="shader: html; target: #youtube-iframe; fps: 30"
    width="1.6"
    height="0.9">
</a-plane>

<!-- 隱藏的 YouTube iframe -->
<iframe id="youtube-iframe" src="YouTube網址"></iframe>
```

---

## 🔧 如何使用

### 第 1 步：取得 YouTube 影片 ID

YouTube 網址格式：
```
https://www.youtube.com/watch?v=dQw4w9WgXcQ
                                 ^^^^^^^^^^^
                                這就是影片 ID
```

或者從分享連結：
```
https://youtu.be/dQw4w9WgXcQ
                 ^^^^^^^^^^^
                這也是影片 ID
```

### 第 2 步：修改 HTML

找到這一行（大約第 48 行）：
```javascript
const YOUTUBE_VIDEO_ID = 'dQw4w9WgXcQ';  // ← 改這裡！
```

把 `'dQw4w9WgXcQ'` 改成你的影片 ID：
```javascript
const YOUTUBE_VIDEO_ID = '你的影片ID';
```

### 第 3 步：上傳並測試

只需要 2 個檔案：
```
📁 資料夾/
├── ar-youtube.html
└── targets.mind
```

上傳到 GitHub Pages 就可以用了！

---

## 🎬 核心技術解析

### 1. aframe-html-shader 組件

這個組件的作用是將 **HTML 元素（iframe）渲染成 3D 材質**：

```html
<script src="https://unpkg.com/aframe-html-shader@0.2.0/dist/aframe-html-shader.min.js"></script>
```

### 2. YouTube IFrame API

使用官方的 YouTube Player API 來控制播放：

```javascript
// 載入 API
const tag = document.createElement('script');
tag.src = "https://www.youtube.com/iframe_api";

// 初始化播放器
player = new YT.Player('youtube-iframe', {
    videoId: 'dQw4w9WgXcQ',
    events: {
        'onReady': onPlayerReady,
        'onStateChange': onPlayerStateChange
    }
});
```

### 3. 材質配置

```html
material="shader: html; target: #youtube-iframe; fps: 30"
```

參數說明：
- `shader: html` - 使用 HTML shader
- `target: #youtube-iframe` - 渲染哪個 iframe
- `fps: 30` - 每秒更新 30 幀（影響流暢度和效能）

---

## ⚙️ 自訂設定

### 調整播放器大小

```html
<a-plane
    width="1.6"    ← 寬度
    height="0.9"   ← 高度
```

常見比例：
- 16:9 → `width="1.6" height="0.9"`
- 4:3 → `width="1.6" height="1.2"`
- 正方形 → `width="1.2" height="1.2"`

### 調整更新幀率

```html
material="shader: html; target: #youtube-iframe; fps: 30"
                                                      ^^
```

- `fps: 60` - 更流暢，但更耗電
- `fps: 30` - 平衡（推薦）
- `fps: 15` - 省電，但可能卡頓

### 自動播放設定

找到這段（第 225 行）：
```javascript
playerVars: {
    'autoplay': 0,  ← 0=不自動播放, 1=自動播放
    'controls': 1,  ← 0=隱藏控制列, 1=顯示控制列
    'mute': 0       ← 可加入這行：0=有聲音, 1=靜音
}
```

⚠️ **注意**：大多數瀏覽器會阻擋自動播放有聲音的影片！

### 修改播放器外觀

YouTube 提供一些自訂參數：
```javascript
playerVars: {
    'autoplay': 0,
    'controls': 1,
    'modestbranding': 1,  // 隱藏 YouTube logo
    'rel': 0,             // 結束時不顯示相關影片
    'showinfo': 0,        // 隱藏影片資訊
    'loop': 1,            // 循環播放
    'playlist': 'VIDEO_ID' // loop 需要這個
}
```

---

## 🎮 控制功能

### 點擊平面播放/暫停
```javascript
youtubePlane.addEventListener('click', () => {
    if (isPlaying) {
        player.pauseVideo();
    } else {
        player.playVideo();
    }
});
```

### 使用按鈕控制
右上角的 ▶️ 按鈕可以：
- 播放影片
- 暫停影片
- 重播影片（結束時）

### YouTube API 控制方法

```javascript
player.playVideo();           // 播放
player.pauseVideo();          // 暫停
player.stopVideo();           // 停止
player.seekTo(秒數);          // 跳轉到指定時間
player.mute();                // 靜音
player.unMute();              // 取消靜音
player.setVolume(音量);       // 設定音量 0-100
player.getPlayerState();      // 取得播放狀態
```

---

## 🐛 常見問題

### ❌ 影片無法播放

**原因 1：影片 ID 錯誤**
```javascript
// ❌ 錯誤：包含整個網址
const YOUTUBE_VIDEO_ID = 'https://www.youtube.com/watch?v=dQw4w9WgXcQ';

// ✅ 正確：只要 ID
const YOUTUBE_VIDEO_ID = 'dQw4w9WgXcQ';
```

**原因 2：影片不允許嵌入**
有些影片禁止在外部網站播放，會顯示錯誤代碼 101 或 150。
解決方法：選擇其他允許嵌入的影片。

**原因 3：影片被移除或設為私人**
檢查影片是否公開可見。

### ❌ 畫面模糊或卡頓

**解決方法 1：調整 FPS**
```html
material="shader: html; target: #youtube-iframe; fps: 30"
```
降低 fps 值可以改善效能。

**解決方法 2：調整 iframe 解析度**
```html
<iframe 
    width="1280"   ← 降低這個
    height="720">  ← 和這個
</iframe>
```

建議解析度：
- 高品質：1920x1080
- 標準：1280x720 （推薦）
- 低品質：854x480

### ❌ 聲音無法播放

**原因**：瀏覽器的自動播放政策

**解決方法**：
1. 使用者必須先點擊畫面
2. 或加入靜音自動播放：
```javascript
playerVars: {
    'autoplay': 1,
    'mute': 1  // 靜音自動播放
}
```

### ❌ AR 辨識後影片沒出現

**檢查清單**：
1. 確認 `aframe-html-shader` 已載入
2. 確認 iframe ID 和 target 一致
3. 檢查瀏覽器 Console 的錯誤訊息
4. 確認 YouTube API 已載入完成

---

## 🎨 進階應用

### 1. 顯示多個 YouTube 影片

```html
<!-- 影片 1 -->
<a-plane
    position="-0.9 0 0"
    material="shader: html; target: #youtube1; fps: 30"
    width="0.8" height="0.45">
</a-plane>

<!-- 影片 2 -->
<a-plane
    position="0.9 0 0"
    material="shader: html; target: #youtube2; fps: 30"
    width="0.8" height="0.45">
</a-plane>

<!-- iframe 容器 -->
<div id="youtube-container">
    <iframe id="youtube1" ...></iframe>
    <iframe id="youtube2" ...></iframe>
</div>
```

### 2. 加入影片進度條

```javascript
// 監聽播放時間
setInterval(() => {
    if (player && player.getCurrentTime) {
        const currentTime = player.getCurrentTime();
        const duration = player.getDuration();
        const progress = (currentTime / duration) * 100;
        console.log('進度:', progress + '%');
    }
}, 1000);
```

### 3. 播放清單功能

```javascript
const playlist = ['VIDEO_ID_1', 'VIDEO_ID_2', 'VIDEO_ID_3'];
let currentIndex = 0;

function playNext() {
    currentIndex = (currentIndex + 1) % playlist.length;
    player.loadVideoById(playlist[currentIndex]);
}

// 影片結束時自動播放下一個
function onPlayerStateChange(event) {
    if (event.data == YT.PlayerState.ENDED) {
        playNext();
    }
}
```

### 4. 同步多個特徵圖

使用不同的 `targetIndex`：

```html
<!-- 特徵圖 1 - 播放影片 A -->
<a-entity mindar-image-target="targetIndex: 0">
    <a-plane material="shader: html; target: #youtubeA"></a-plane>
</a-entity>

<!-- 特徵圖 2 - 播放影片 B -->
<a-entity mindar-image-target="targetIndex: 1">
    <a-plane material="shader: html; target: #youtubeB"></a-plane>
</a-entity>
```

---

## 🔍 錯誤代碼對照表

YouTube API 錯誤代碼：

| 代碼 | 意義 | 解決方法 |
|------|------|---------|
| 2 | 請求包含無效的參數 | 檢查影片 ID 格式 |
| 5 | HTML5 播放器錯誤 | 換個瀏覽器試試 |
| 100 | 影片不存在或被移除 | 確認影片還存在 |
| 101 | 影片擁有者不允許嵌入 | 換其他影片 |
| 150 | 同 101 | 換其他影片 |

---

## 📊 效能比較

| 方案 | 優點 | 缺點 |
|------|------|------|
| 本地 MP4 | • 載入快<br>• 控制完整<br>• 無網路限制 | • 檔案大<br>• 需上傳影片 |
| YouTube | • 不需上傳影片<br>• 自動適應網速<br>• 支援高清 | • 需要網路<br>• 可能被限制嵌入<br>• 效能稍差 |

---

## 💡 最佳實踐

1. **選擇合適的影片**
   - 確認影片允許嵌入
   - 避免太長的影片（建議 < 3 分鐘）
   - 選擇高品質但不過大的影片

2. **優化效能**
   - fps 設為 30（不要太高）
   - iframe 解析度設為 1280x720
   - 追蹤丟失時暫停播放

3. **改善用戶體驗**
   - 提供清楚的播放提示
   - 加入載入動畫
   - 處理錯誤並顯示友善訊息

4. **測試**
   - 在不同裝置上測試
   - 檢查不同網速下的表現
   - 確認聲音和畫面同步

---

## 🆚 與本地影片的差異

| 項目 | 本地 MP4 | YouTube |
|------|----------|---------|
| 標籤 | `<a-video>` | `<a-plane>` + iframe |
| 組件 | 內建 | aframe-html-shader |
| 檔案 | video.mp4 | 不需要檔案 |
| 載入 | 一次載入完整影片 | 串流播放 |
| 控制 | 直接控制 video 元素 | YouTube API |

---

## 📚 參考資源

- **aframe-html-shader**: https://github.com/mayognaise/aframe-html-shader
- **YouTube IFrame API**: https://developers.google.com/youtube/iframe_api_reference
- **MindAR**: https://hiukim.github.io/mind-ar-js-doc/

---

## 🎓 小測驗

改成你自己的影片，需要修改幾個地方？

**答案：只要 1 個地方！**

```javascript
const YOUTUBE_VIDEO_ID = '改這裡就好！';
```

就是這麼簡單！🎉

---

需要其他功能嗎？例如：
- 播放 Vimeo 影片
- 加入字幕
- 多國語言切換
- 播放控制列

隨時告訴我！
