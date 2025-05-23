
# YOLOv5 NCNN Deployment on NVIDIA Jetson Nano

This repository provides a C++ demo and model files for running YOLOv5 on a Jetson Nano using the NCNN inference framework. It includes build scripts, step-by-step instructions, common error fixes, and tips for maximizing real-time performance.

---

## 📂 Repository Structure

```
YoloV5-ncnn-Jetson-Nano/
├── yolov5.cpp            # Main C++ demo source
├── yolov5s.param         # NCNN model parameters
├── yolov5s.bin           # NCNN model weights
├── yolov5s-opt.param     # Optimized NCNN parameters
├── yolov5s-opt.bin       # Optimized NCNN weights
├── CMakeLists.txt        # Build configuration for demo
├── build/                # Out‑of‑source build artifacts
├── scripts/              # Utility scripts (e.g. model fusion)
└── README.md             # This file
```

---

## 🚀 Quickstart

1. **Clone the repo**

   ```bash
   git clone https://github.com/Himanshu040604/YoloV5-ncnn-Jetson-Nano.git
   cd YoloV5-ncnn-Jetson-Nano
   ```

2. **Install dependencies**

   ```bash
   sudo apt-get update
   sudo apt-get install -y \
     build-essential cmake git \
     libopencv-dev libopencv-core-dev libopencv-highgui-dev \
     libprotobuf-dev protobuf-compiler \
     libvulkan1 libvulkan-dev vulkan-utils \
     libopenblas-dev libgomp1
   ```

3. **Build NCNN** (if not already installed)

   ```bash
 
   cd ~/ncnn
   mkdir -p build && cd build
   cmake -DNCNN_VULKAN=OFF -DNCNN_OPENMP=ON -DCMAKE_INSTALL_PREFIX=/usr/local ..
   make -j4
   sudo make install
   ```

4. **Build the demo**

   ```bash
   mkdir -p build && cd build
   cmake ..
   make -j4
   ```

5. **Copy model files into `build/`**

   ```bash
   cp ../yolov5s-opt.param .
   cp ../yolov5s-opt.bin   .
   ```

6. **Run real-time camera demo**

   ```bash
   ./yolov5_ncnn 0
   ```

7. **Test on an image**

   ```bash
   ./yolov5_ncnn ../busstop.jpg
   ```

---

## ⚙️ Optimization Tips

* **Model Fusion**: use `ncnnoptimize` to fuse conv+activation and reduce runtime overhead:

  ```bash
  ncnnoptimize \
    yolov5s.param yolov5s.bin \
    yolov5s-opt.param yolov5s-opt.bin 0
  ```
* **Vulkan vs. CPU**: toggle GPU inference in code:

  ```cpp
  yolov5.opt.use_vulkan_compute = true;   
  yolov5.opt.use_vulkan_compute = false; 
  yolov5.opt.num_threads = 4;            
  ```
* **Precision**: enable FP16 on NCNN (if supported) or build a TensorRT FP16 engine for >2× speedup.
* **Power Mode**: maximize Jetson Nano clocks:

  ```bash
  sudo nvpmodel -m 0
  sudo jetson_clocks
  ```

---

## 🛠 Common Errors & Fixes

* **`layer.h: No such file or directory`**

  * Ensure `/usr/local/include/ncnn` is in `include_directories` of `CMakeLists.txt`.

* **`cv2` module not found**

  * Install Python OpenCV: `sudo apt-get install python3-opencv`.

* **`parse magic failed` in `ncnnoptimize`**

  * Restore a valid `yolov5s.param` (nonzero size, first line `7767517`).

* **Camera usage message**

  * Update `main()` to default to camera index 0 when `argc == 1` and detect numeric args.

---

## 📚 Further Reading

* **NCNN Documentation**: [https://github.com/Tencent/ncnn/wiki](https://github.com/Tencent/ncnn/wiki)
* **YOLOv5 Repo**: [https://github.com/ultralytics/yolov5](https://github.com/ultralytics/yolov5)
* **Jetson Nano Dev Guide**: [https://docs.nvidia.com/jetson/](https://docs.nvidia.com/jetson/)



