# Build Notes

How to get this running, and the order I built it in.

## Build and run

You'll need a Windows machine with an NVIDIA GPU. I used MSVC, Ninja, CUDA 13.3, TensorRT 10.16, OpenCV 4.12, Redis, PostgreSQL 17, Qt 6.11 (MinGW) and vcpkg, and the other C++ libraries are listed in `vcpkg.json`.

The model in `engines/` was built for my GPU and TensorRT version, so you'll probably need to export it again on a different setup.

Build the back end and the desktop app first, changing the paths to match your machine:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\build_backend.ps1 -CleanFirst `
  -InstallDependencies `
  -VcpkgRoot D:\vcpkg `
  -CudaRoot D:\CUDA `
  -TensorRtRoot D:\TensorRT `
  -OpenCvDir D:\opencv\build\x64\vc16\lib

powershell -ExecutionPolicy Bypass -File .\scripts\build_qt.ps1 `
  -QtRoot D:\qt\6.11.1\mingw_64 -CleanFirst
```

Start Redis and PostgreSQL, then run:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\start_demo.ps1
```

It'll ask for the camera address, an admin token and the database connection without showing them on screen, then open the desktop app. Click 检查服务 (check services), then 启动会话 (start session). You can manage cameras at <http://127.0.0.1:8087/camera-admin>.

To stop it or run the tests:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\stop_demo.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\test_all.ps1
```

## How I built it

I started from a bigger vision project and cut it down to one model (YOLO11n-pose), one kind of analysis (people flow) and a small desktop app, then built the rest up one step at a time.

1. First I froze the baseline and the design so I had a fixed place to start from.
2. Then I got each camera decoding just once with FFmpeg and passing the frames to every job that needs them.
3. I moved the existing people-flow analysis onto that shared reader.
4. After that I made one worker process able to run all the different jobs.
5. Next I added task storage, commands through Redis and a job that saves JPEG frames and clears out old ones.
6. Then came an HTTP API with token login and diagnostics, and a web page for managing cameras.
7. To keep it running I added monitoring, failure recovery, storage limits, and backup and restore.
8. Finally I gave every camera a permanent ID and moved the data from SQLite to PostgreSQL.
