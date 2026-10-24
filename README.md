# Scroll Glitch Hero

**[在线演示 / Live demo →](https://codermaurice.github.io/scroll-glitch-hero/)**

[中文](#中文) · [English](#english)

![Hero](docs/hero.jpg)

![Pointer interaction](docs/pointer.png)

![Transition](docs/transition.jpg)

---

## 中文

一个滚动触发的首屏转场动效：往下一滑，雾中山景被撕成一条条横向故障带，代码字符在碎片里一闪而过，画面按方块逐块溶解，下面的正文同时浮上来。滚回顶部，动画倒着播放，山景重新拼合。

灵感来自 Kimi K3.1 Pro 技术报告官网首页的转场效果。这是一个独立的重新实现：山景是程序生成的，着色器和脚本都是从头写的，没有使用原站的任何代码或素材。

### 特点

- **单个 HTML 文件**：没有构建步骤，也没有依赖，双击就能打开
- **程序生成的雾山**：WebGL2 着色器实时绘制，不需要图片素材，鼠标移动有视差
- **淡淡的代码层**：整个首屏铺着一层很淡的代码，一直都在，按缓慢漂移的斑块明暗起伏。在深色山体上看得清，在亮云里几乎消失
- **鼠标搅动代码**：鼠标划过的地方，代码会稍微亮一点、颜色偏绿一点，并像打字一样向右延伸；头部字符会先乱码闪一下再定格
- **雾气被推开**：标题上飘着一层薄雾，鼠标经过时把雾和文字往移动方向推开，字像烟一样散开，之后雾慢慢合拢
- **故障转场**：粗、细两层撕裂带、横向拉丝、RGB 色散、重影，部分撕裂带上印着滚动的代码
- **方块溶解**：画布透明度按块打开，露出下面真实的 DOM 正文（文字可选中，SEO 友好）
- **可逆**：从正文滚回顶部会反向播放
- **降级处理**：开启"减少动态效果"或不支持 WebGL2 时，退回普通的静态滚动

### 使用

在线体验：[https://codermaurice.github.io/scroll-glitch-hero/](https://codermaurice.github.io/scroll-glitch-hero/)（建议用电脑打开，往下滚一下；滚回顶部会反向播放）

本地运行：

```bash
git clone https://github.com/CoderMaurice/scroll-glitch-hero.git
cd scroll-glitch-hero
open index.html
```

也可以起一个本地服务器：`python3 -m http.server`，然后访问 `http://localhost:8000`。

### 自定义

`index.html` 脚本开头的 `CONFIG`：

| 字段 | 说明 |
| --- | --- |
| `title` / `subtitle` / `tagline` | 首屏文字 |
| `duration` | 转场时长（毫秒），默认 900 |
| `cooldown` | 转场结束后锁定输入的时长（毫秒） |
| `glitchAmount` | 撕裂强度，0–1 |
| `codeAmbient` | 代码层平时的可见度，0–1 |
| `titleVeil` | 标题上薄雾的浓度，0–1 |

故障条里出现的代码在 `CODE` 数组里。在网址后面加 `?slow=10`，转场会慢放 10 倍，方便逐帧观察。

### 原理

1. **滚动锁定**：页面滚动超过 8px 时开始一段固定时长的时间线（进度 0→1），期间拦截滚轮、触摸和方向键，把滚动位置钉住。结束后跳到正文顶部。
2. **两个渲染通道**：第一个通道把雾山画进一张低分辨率离屏纹理；第二个通道叠上标题，再做故障处理，直接输出到屏幕。
3. **撕裂带**：每一行切成宽度随机的格子，由一层低频噪声决定哪些区域"活跃"，活跃格子做水平位移、拉丝和色散。
4. **溶解**：输出的 alpha 按方块逐渐变为 0，大致从底部开始、成片打开。因为用的是预乘 alpha，透明处直接露出下面的 HTML。
5. **正文入场**：通过 CSS 变量 `--reveal` 驱动正文的淡入和上移。
6. **代码层**：屏幕划成等宽字符格，预先把整面代码墙画进一张纹理（R 通道是代码，G 通道是乱码）。代码平时的可见度由一层缓慢漂移的噪声控制。鼠标路径上随机生成若干段代码，CPU 每帧把它们写进一张很小的 RG8 网格纹理，被搅动的格子更亮、颜色偏绿。
7. **推雾**：一张粗网格（RGBA16F）记录鼠标推动的位移和清开雾的程度。每次移动在光标周围按高斯分布叠加位移，每帧向邻格扩散并衰减。着色器用它扭曲标题和雾的采样位置，再加一点噪声做出烟丝感，并按清开程度减弱标题上的薄雾。

### 许可

[MIT](LICENSE)

本项目与 Kimi / Moonshot AI 没有任何关联。

---

## English

A scroll-triggered hero transition. Scroll down once and the misty mountain scene tears apart into horizontal glitch bands, with flashes of source code inside the fragments. The frame then dissolves block by block while the article underneath rises into view. Scroll back to the top and it plays in reverse, reassembling the scene.

Inspired by the hero transition on the Kimi K3.1 Pro tech-report page. This is an independent re-implementation: the scenery is procedural, and the shaders and script are written from scratch. No code or assets from the original site are used.

### Features

- **Single HTML file**: no build step and no dependencies, just open it
- **Procedural misty mountains**: drawn live by a WebGL2 shader with no image assets, plus pointer parallax
- **Faint code layer**: a thin wall of code is always there across the hero, breathing in slowly drifting patches. It reads over dark rock and melts away into bright cloud
- **Pointer stirs the code**: where the mouse passes, the code gets a little stronger and greener and types itself out to the right. Glyphs at the head scramble briefly before settling
- **Mist pushed aside**: a veil of mist drifts across the title. The pointer shoves the mist and the letters along its path so they wisp like smoke, then the mist slowly closes back
- **Glitch transition**: two layers of tears (fine and coarse), horizontal smears, RGB split and ghosting, with scrolling code printed on some of the tears
- **Block dissolve**: canvas alpha opens in blocks to reveal the real DOM article underneath (selectable text, SEO-friendly)
- **Reversible**: scrolling back up from the article plays the transition backwards
- **Graceful fallback**: with `prefers-reduced-motion` or without WebGL2 it falls back to a plain static page

### Usage

Try it online: [https://codermaurice.github.io/scroll-glitch-hero/](https://codermaurice.github.io/scroll-glitch-hero/). A desktop browser works best: scroll down once, then scroll back to the top to see it reverse.

Run locally:

```bash
git clone https://github.com/CoderMaurice/scroll-glitch-hero.git
cd scroll-glitch-hero
open index.html
```

Or serve it locally with `python3 -m http.server` and visit `http://localhost:8000`.

### Customize

`CONFIG` at the top of the script in `index.html`:

| Key | Description |
| --- | --- |
| `title` / `subtitle` / `tagline` | Hero text |
| `duration` | Transition length in ms (default 900) |
| `cooldown` | How long input stays locked after the transition, in ms |
| `glitchAmount` | Tear intensity, 0–1 |
| `codeAmbient` | How visible the code layer is at rest, 0–1 |
| `titleVeil` | How much drifting mist covers the title, 0–1 |

The code shown inside the tears lives in the `CODE` array. Append `?slow=10` to the URL to play the transition 10× slower and inspect it frame by frame.

### How it works

1. **Scroll lock**: once the page scrolls past 8px, a fixed-length timeline starts (progress 0→1). While it runs, wheel, touch and arrow-key input are intercepted and the scroll position is pinned. When it ends, the page jumps to the top of the article.
2. **Two render passes**: the first pass draws the mountains into a low-resolution offscreen texture. The second composites the title, applies the glitch and outputs straight to the screen.
3. **Tears**: each row is split into cells of random width. A low-frequency noise field decides which areas are active, and active cells get shifted, smeared and colour-split.
4. **Dissolve**: output alpha falls to zero block by block, in ragged patches that roughly start from the bottom. Premultiplied alpha lets the HTML below show through.
5. **Article entrance**: a CSS variable, `--reveal`, drives the article's fade and rise.
6. **Code layer**: the screen is a monospace cell grid, and a full wall of code is pre-rendered into one texture (code in red, scramble glyphs in green). A slowly drifting noise sets how visible it is at rest. Pointer movement spawns short runs, which the CPU writes each frame into a tiny RG8 grid texture; stirred cells get brighter and greener.
7. **Mist push**: a coarse RGBA16F grid stores how far the pointer has shoved things and how much mist it has cleared. Each move splats a Gaussian of displacement around the cursor, and each frame it diffuses to neighbours and decays. The shader offsets where the title and mist are sampled, adds a little noise for smoky wisps, and thins the title veil where it's been cleared.

### License

[MIT](LICENSE)

Not affiliated with Kimi or Moonshot AI.
