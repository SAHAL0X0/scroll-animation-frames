# Scroll Animation Frames

High-quality animation frame sequence (synapse / neural network firing) ready for scroll-driven canvas animations in **Lovable**, React, Vite, Next.js, or vanilla web projects.

---

## 📊 Overview

- **Total Frames:** 300 (`ezgif-frame-001.jpg` to `ezgif-frame-300.jpg`)
- **File Format:** JPG
- **Sequence Integrity:** Complete sequential index (1 to 300) with zero missing frames
- **Folder:** `/frames/`

---

## 🌐 Frame URL Formats

### 1. Direct GitHub Raw URL
```text
https://raw.githubusercontent.com/SAHAL0X0/scroll-animation-frames/main/frames/ezgif-frame-{index:03d}.jpg
```
Example frame 1:
```text
https://raw.githubusercontent.com/SAHAL0X0/scroll-animation-frames/main/frames/ezgif-frame-001.jpg
```

### 2. Fast CDN URL (jsDelivr - Recommended for Production)
For high-performance scroll animations, jsDelivr offers global edge caching and avoids GitHub rate-limits:
```text
https://cdn.jsdelivr.net/gh/SAHAL0X0/scroll-animation-frames@main/frames/ezgif-frame-{index:03d}.jpg
```
Example frame 1:
```text
https://cdn.jsdelivr.net/gh/SAHAL0X0/scroll-animation-frames@main/frames/ezgif-frame-001.jpg
```

---

## 📁 Repository Structure

```text
scroll-animation-frames/
├── frames/
│   ├── ezgif-frame-001.jpg
│   ├── ezgif-frame-002.jpg
│   └── ... (up to ezgif-frame-300.jpg)
├── frames.json            # Array of all 300 frame URLs in sequence
├── frames-manifest.json   # Metadata + full frame array
└── README.md
```

---

## 🚀 How to Use in Lovable (React Canvas Scroll Animation)

In your Lovable project (or React / Vite app), create a component named `ScrollCanvasAnimation.tsx`:

```tsx
import React, { useEffect, useRef, useState } from "react";

const TOTAL_FRAMES = 300;
const getFrameUrl = (index: number) => {
  const paddedIndex = String(index).padStart(3, "0");
  return `https://cdn.jsdelivr.net/gh/SAHAL0X0/scroll-animation-frames@main/frames/ezgif-frame-${paddedIndex}.jpg`;
};

export const ScrollCanvasAnimation: React.FC = () => {
  const canvasRef = useRef<HTMLCanvasElement | null>(null);
  const containerRef = useRef<HTMLDivElement | null>(null);
  const [images, setImages] = useState<HTMLImageElement[]>([]);
  const [loaded, setLoaded] = useState(false);

  // Preload all frames for smooth scrolling
  useEffect(() => {
    const loadedImages: HTMLImageElement[] = [];
    let loadCount = 0;

    for (let i = 1; i <= TOTAL_FRAMES; i++) {
      const img = new Image();
      img.src = getFrameUrl(i);
      img.onload = () => {
        loadCount++;
        if (loadCount === TOTAL_FRAMES) {
          setLoaded(true);
        }
      };
      loadedImages.push(img);
    }
    setImages(loadedImages);
  }, []);

  // Render on scroll
  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext("2d");
    if (!ctx) return;

    const render = () => {
      if (!containerRef.current || images.length === 0) return;
      const rect = containerRef.current.getBoundingClientRect();
      const scrollProgress = Math.min(
        Math.max(-rect.top / (rect.height - window.innerHeight), 0),
        1
      );

      const frameIndex = Math.min(
        Math.floor(scrollProgress * (TOTAL_FRAMES - 1)),
        TOTAL_FRAMES - 1
      );

      const img = images[frameIndex];
      if (img && img.complete) {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        const hRatio = canvas.width / img.width;
        const vRatio = canvas.height / img.height;
        const ratio = Math.max(hRatio, vRatio);
        const centerShiftX = (canvas.width - img.width * ratio) / 2;
        const centerShiftY = (canvas.height - img.height * ratio) / 2;
        ctx.drawImage(
          img,
          0,
          0,
          img.width,
          img.height,
          centerShiftX,
          centerShiftY,
          img.width * ratio,
          img.height * ratio
        );
      }
    };

    window.addEventListener("scroll", render, { passive: true });
    window.addEventListener("resize", render);
    render();

    return () => {
      window.removeEventListener("scroll", render);
      window.removeEventListener("resize", render);
    };
  }, [images]);

  return (
    <div ref={containerRef} className="relative h-[400vh] w-full bg-black">
      <div className="sticky top-0 h-screen w-full flex items-center justify-center overflow-hidden">
        <canvas
          ref={canvasRef}
          width={1920}
          height={1080}
          className="w-full h-full object-cover"
        />
        {!loaded && (
          <div className="absolute inset-0 flex items-center justify-center bg-black/80 text-white font-mono">
            Loading animation frames...
          </div>
        )}
      </div>
    </div>
  );
};
```
