# The Archives - Documentation Wiki

## Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [User Guide](#user-guide)
4. [Technical Details](#technical-details)
5. [Mobile Optimization](#mobile-optimization)
6. [Security & Privacy](#security--privacy)
7. [Troubleshooting](#troubleshooting)
8. [Contributing](#contributing)

---

## Overview

**The Archives** is a local media explorer web application that provides a cinematic, immersive interface for browsing and previewing files stored on your device. It operates entirely client-side, ensuring your files never leave your device.

### Key Principles

- **Privacy-First Design**: All operations are local, no server uploads
- **Universal Compatibility**: Works on all modern browsers and devices
- **Performance-Optimized**: Targets 60 FPS with efficient rendering
- **Accessible**: Keyboard shortcuts and touch-friendly controls

---

## Architecture

### File Structure

```
├── index.html          # Main application (single-page app)
├── AnotherVersion.html # Alternative version of the app
├── README.md           # Project documentation
├── LICENSE             # MIT License
└── docs/
    ├── WIKI.md         # This documentation
    └── screenshots/    # Application screenshots
```

### Technology Stack

| Layer | Technology |
|-------|------------|
| UI Framework | Tailwind CSS (CDN) |
| Fonts | Google Fonts (Cinzel, Lato) |
| File Access | File System Access API |
| Media Playback | Native HTML5 Video/Audio |
| Animation | Canvas 2D + requestAnimationFrame |
| Visualization | Web Audio API |

### Application States

```
┌─────────────┐     ┌──────────────────┐     ┌───────────────┐
│  Landing    │ ──► │  File Explorer   │ ──► │ Media Preview │
│    Page     │     │  (Grid/List)     │     │   (Overlay)   │
└─────────────┘     └──────────────────┘     └───────────────┘
       │                    │                        │
       │                    ▼                        │
       │            ┌──────────────┐                 │
       └───────────►│ Session End  │◄────────────────┘
                    └──────────────┘
```

---

## User Guide

### Getting Started

1. **Open the Application**
   - Visit the GitHub Pages URL or serve locally
   - The landing page displays with ambient particle effects

2. **Select Files**
   - **Open Folder**: Browse an entire directory
   - **Select Files**: Pick specific files

3. **Browse Your Files**
   - Use Grid View for visual browsing with thumbnails
   - Use List View for detailed file information
   - Sort by name, size, or date

4. **Preview Media**
   - Click any file to open the fullscreen preview
   - Use controls for video/audio playback
   - Navigate with Previous/Next buttons

### Search Features

| Search Type | Example | Description |
|-------------|---------|-------------|
| Name | `vacation` | Files containing "vacation" |
| Extension | `.mp4` | All MP4 files |
| Type | `video` | All video files |
| Year | `2024` | Files from 2024 |

### Supported File Types

#### Full Preview Support
- **Images**: JPG, JPEG, PNG, GIF, BMP, WebP, SVG, HEIF, HEIC, TIFF
- **Videos**: MP4, WebM, MKV, MOV, AVI, M4V, FLV, MPG, MPEG
- **Audio**: MP3, WAV, OGG, FLAC, M4A, AAC
- **Documents**: PDF, TXT, JSON, CSV, HTML, CSS, JS

#### Icon-Only Preview
- Archives: ZIP, RAR, 7Z, TAR, GZ
- Executables: EXE, MSI, APP, APK
- Fonts: TTF, OTF, WOFF, WOFF2
- Databases: SQL, DB, SQLite
- 3D Models: OBJ, FBX, STL, BLEND

---

## Technical Details

### Performance Optimizations

#### 60 FPS Animation System

```javascript
// Frame rate limiting
const frameInterval = 1000 / 60;
function animateBg(time) {
    const elapsed = time - lastFrameTime;
    if (elapsed < frameInterval) {
        requestAnimationFrame(animateBg);
        return;
    }
    lastFrameTime = time - (elapsed % frameInterval);
    // Render frame
}
```

#### Lazy Loading

- File grid uses IntersectionObserver for lazy loading
- Batch rendering (50 items at a time)
- Virtual scrolling for large file lists

#### Mobile Optimizations

- Reduced particle count on mobile (75 vs 150)
- Disabled shadow blur effects on touch devices
- Pause animation when page is not visible

### API Usage

#### File System Access API (Modern Browsers)

```javascript
// Directory selection
const dirHandle = await window.showDirectoryPicker();

// File selection
const handles = await window.showOpenFilePicker({ multiple: true });
```

#### Fallback for Older Browsers

```html
<input type="file" webkitdirectory directory multiple>
<input type="file" multiple>
```

### Web Audio API (Visualizer)

```javascript
const audioCtx = new AudioContext();
const analyser = audioCtx.createAnalyser();
analyser.fftSize = 256;
// Connect media element
const src = audioCtx.createMediaElementSource(audioElement);
src.connect(analyser);
analyser.connect(audioCtx.destination);
```

---

## Mobile Optimization

### Responsive Breakpoints

| Breakpoint | Target Devices |
|------------|----------------|
| < 480px | Small phones (portrait) |
| < 768px | Standard phones |
| 768px - 1024px | Tablets |
| > 1024px | Desktops |

### Aspect Ratio Support

#### 16:9 Devices (Standard)
- Standard smartphones and tablets
- Optimized grid columns (130px minimum)

#### 20:9 / 21:9 Devices (Modern Android)
- Tall-screen phones
- Compact header with smaller controls
- Optimized for vertical scrolling

#### Landscape Mode
- Horizontal layout for phones
- Side-by-side button arrangement
- Reduced control bar height

### Touch Optimizations

- **Touch targets**: Minimum 44x44px for buttons
- **Tap highlight**: Disabled for native-like feel
- **Double-tap zoom**: Prevented via `touch-action: manipulation`
- **Safe area insets**: Support for notched devices

---

## Security & Privacy

### Data Safety Guarantee

1. **Read-Only Access**
   - The File System Access API is used with read permissions only
   - No write, modify, or delete operations are performed

2. **No Network Transmission**
   - Files are never uploaded to any server
   - No analytics or tracking
   - Works completely offline after initial load

3. **Temporary Object URLs**
   - Files are accessed via `URL.createObjectURL()`
   - URLs are automatically revoked when no longer needed
   - No persistent storage of file data

4. **Memory-Only Processing**
   - All file processing happens in browser memory
   - No data is written to disk or localStorage
   - Session ends completely on page close

### CSP Recommendations

For enhanced security when self-hosting:

```html
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; 
               style-src 'self' 'unsafe-inline' https://cdn.tailwindcss.com https://fonts.googleapis.com; 
               script-src 'self' 'unsafe-inline' https://cdn.tailwindcss.com; 
               font-src https://fonts.gstatic.com;
               img-src 'self' blob:;
               media-src 'self' blob:;">
```

---

## Troubleshooting

### Common Issues

#### "Folder selection not working"
- **Cause**: Browser doesn't support File System Access API
- **Solution**: Use Chrome/Edge, or use "Select Files" instead

#### "Videos not playing"
- **Cause**: Unsupported codec
- **Solution**: Convert to MP4 (H.264) or WebM

#### "Slow performance on mobile"
- **Cause**: Too many large files
- **Solution**: Browse smaller batches of files

#### "Audio visualizer not showing"
- **Cause**: AudioContext blocked
- **Solution**: Click play button first to enable audio

### Browser-Specific Notes

#### Firefox
- Folder selection requires fallback input
- Some File System API features limited

#### Safari
- Add to Home Screen for best PWA experience
- Some video formats may not be supported

#### Mobile Chrome
- Enable "Desktop site" for folder selection
- Touch-to-play may be required for videos

---

## Contributing

### Development Setup

1. Clone the repository
2. Serve with any static file server
3. Test on multiple devices and browsers

### Code Style

- Vanilla JavaScript (no frameworks)
- CSS-in-HTML approach for single-file distribution
- Minimal dependencies (Tailwind via CDN)

### Testing Checklist

Before submitting changes:

- [ ] Test on Chrome, Firefox, Safari
- [ ] Test on mobile devices (Android + iOS)
- [ ] Verify 60 FPS animation
- [ ] Check memory usage
- [ ] Verify read-only file access
- [ ] Test keyboard navigation
- [ ] Test touch controls

---

*Last updated: December 2025*
