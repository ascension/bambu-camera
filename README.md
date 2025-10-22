# BambuLab Camera Streaming Protocols

> **Complete guide to implementing camera streaming for all BambuLab 3D printer models**

![BambuLab](https://img.shields.io/badge/BambuLab-All_Models-blue)
![Protocol](https://img.shields.io/badge/Protocol-RTSP%20%7C%20Native-green)
![Status](https://img.shields.io/badge/Status-Production_Ready-success)

## 🎯 Quick Start

### X1/H2 Series (Simple - Uses Standard RTSP)

```bash
# View in VLC
vlc rtsps://bblp:<access_code>@<ip>:322/streaming/live/1

# Stream to browser with FFmpeg
ffmpeg -rtsp_transport tcp -tls_verify 0 \
  -i "rtsps://bblp:<access_code>@<ip>:322/streaming/live/1" \
  -f mjpeg http://localhost:8080/
```

**Models**: X1C, X1, X1E, H2S, H2D

### P1/A1 Series (Complex - Requires Native Library)

```bash
# 1. Download library
curl -LOJ https://public-cdn.bambulab.com/upgrade/studio/plugins/01.04.00.15/mac_01.04.00.15.zip
unzip mac_01.04.00.15.zip

# 2. Use provided Python/Node.js implementation
# See examples below
```

**Models**: P1P, P1S, A1, A1M

---

## 🔍 The Key Difference

### X1/H2 Series: Standard Protocol ✅

```
Connect to RTSP → Authenticate → Stream starts automatically
```

Works with **any RTSP client** (VLC, FFmpeg, etc.)

### P1/A1 Series: Proprietary Protocol ⚠️

```
Connect to port 6000 → Authenticate → Send "start stream" command → Stream starts
                                              ↑
                                      REQUIRES libBambuSource
```

**Cannot use standard tools** - must use BambuLab's library or existing implementations.

---

## 📚 Protocol Details

### Common Requirements (All Models)

#### 1. Enable LAN Liveview

```
Printer → Settings → Network → LAN Only Liveview → ON
```

**This is required!** Camera won't stream if this is OFF.

#### 2. MQTT Keep-Alive

All cameras need MQTT commands to stay active:

```python
import paho.mqtt.client as mqtt
import ssl

# Connect to MQTT
client = mqtt.Client()
client.tls_set(tls_version=ssl.PROTOCOL_TLS, cert_reqs=ssl.CERT_NONE)
client.tls_insecure_set(True)
client.username_pw_set('bblp', access_code)
client.connect(printer_ip, 8883)

# Send pushall every 5 seconds
def send_pushall():
    client.publish(f"device/{serial}/request", 
                   json.dumps({"pushing": {"sequence_id": "1", "command": "pushall"}}))

# Keep printer awake
import threading
threading.Timer(5.0, send_pushall).start()
```

---

## 💻 Implementation Guide

### X1/H2 Implementation (Easy)

**[Complete Python Example](examples/x1_camera.py)**

```python
# 60 lines - works with standard FFmpeg
# No special libraries needed!
```

**[Complete Node.js Example](examples/x1_camera.ts)**

```typescript
// 50 lines - uses child_process + FFmpeg
// No special libraries needed!
```

### P1/A1 Implementation (Advanced)

**[Complete Python Example](examples/p1_camera.py)**

```python
# 150 lines - requires libBambuSource
# Uses ctypes to call native library
```

**[Complete Node.js Example](examples/p1_camera.ts)**

```typescript
// 200 lines - requires libBambuSource
// Uses koffi for FFI bindings
```

---

## 🛠️ Tools & Libraries

### For X1/H2 Series

**Standard Tools** (no installation needed):
- FFmpeg
- VLC
- mpv
- Any RTSP client

### For P1/A1 Series

**Required**:
- `libBambuSource` (download from Bambu CDN)

**Language Bindings**:
- **Python**: ctypes (built-in)
- **Node.js**: koffi (npm install koffi)
- **C/C++**: Direct linking

**Existing Libraries**:
- [bambu-connect](https://github.com/mattcar15/bambu-connect) (Python)
- [BambuP1Streamer](https://github.com/slynn1324/BambuP1Streamer) (Docker)

---

## 📖 Documentation

- **[COMPLETE_GUIDE.md](BAMBULAB_CAMERA_STREAMING_README.md)** - Full technical documentation
- **[API_REFERENCE.md](docs/API_REFERENCE.md)** - libBambuSource API
- **[TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)** - Common issues
- **[EXAMPLES](examples/)** - Working code examples

---

## 🤝 Contributing

We welcome contributions!

- **Test** with different printer models
- **Submit** code examples in other languages
- **Report** issues or protocol changes
- **Improve** documentation

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## ⚖️ License

**Documentation**: MIT License

**libBambuSource**: Proprietary (BambuLab) - freely downloadable but not open source?

**Code Examples**: MIT License

---

## 🙏 Acknowledgments

- **BambuLab**: For making library publicly available
- **Community Contributors**: Testing and feedback
- **Open Source Projects**: bambu-connect, BambuP1Streamer
- **BambuStudio**: Reference implementation

---

## 📞 Support

- **Issues**: [GitHub Issues](your-repo/issues)
- **Discussions**: [GitHub Discussions](your-repo/discussions)
- **Forum**: [BambuLab Community](https://forum.bambulab.com)

---

**Star ⭐ this repo if it helped you!**

Made with ❤️ for the 3D printing community

