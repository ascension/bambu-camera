# bambu-camera


# P1/A1 Camera Integration

## Overview
This document describes the integration of native camera streaming for Bambu Lab P1 and A1 series printers.

## Architecture

### Streaming Protocol Selection

```typescript
// Automatic protocol detection based on printer model
if (model === 'P1P' || model === 'P1S' || model === 'A1' || model === 'A1M') {
  // Use native library (port 6000)
  camera = new BambuNativeCamera({ ip, accessCode });
} else if (model === 'X1C' || model === 'X1' || model === 'H2S' || model === 'H2D') {
  // Use RTSP (port 322)
  url = `rtsps://bblp:${accessToken}@${ip}:322/streaming/live/1`;
}
```
