# Canon as Webcam on Linux Mint

## Installation (first time only)

```bash
sudo apt install gphoto2 v4l2loopback-dkms v4l2loopback-utils ffmpeg
```

## Step by step usage

### 1. Load the virtual webcam module

```bash
sudo modprobe v4l2loopback exclusive_caps=1 max_buffers=2 card_label="Canon Webcam"
```

### 2. Connect and turn on the camera via USB

### 3. Start the capture

```bash
gphoto2 --stdout --capture-movie | ffmpeg -i - -vcodec rawvideo -pix_fmt yuv420p -f v4l2 /dev/video0
```

### 4. Use in any app

The camera will show up as **"Canon Webcam"** in Zoom, Teams, OBS, etc.

To stop the capture: press `Ctrl+C` in the terminal.

---

## Quick aliases

Add these to the end of your `~/.bashrc`:

```bash
alias webcam='sudo modprobe v4l2loopback exclusive_caps=1 max_buffers=2 card_label="Canon Webcam" 2>/dev/null; sleep 1; gphoto2 --stdout --capture-movie | ffmpeg -i - -vcodec rawvideo -pix_fmt yuv420p -f v4l2 /dev/video0'
alias webcam-off='sudo fuser -k /dev/video0 2>/dev/null; sudo modprobe -r v4l2loopback 2>/dev/null; gphoto2 --set-config eosremoterelease=None 2>/dev/null && echo "✔ Webcam off" || echo "Turn off Live View manually on the camera"'
```

Then reload with:

```bash
source ~/.bashrc
```

Usage: `webcam` to start, `Ctrl+C` then `webcam-off` to stop.

---

## Troubleshooting

- **"Could not claim the USB device"**: Run `ps aux | grep gphoto` and kill any blocking processes with `kill -9 <PID>`.
- **Camera not detected**: Check with `gphoto2 --auto-detect`.
- **`/dev/video0` doesn't exist**: The v4l2loopback module isn't loaded. Run step 1.
