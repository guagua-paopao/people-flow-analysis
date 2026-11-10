# Build Notes

How to build and run this project, how I put it together, and what I learned.

## Build and run

This project runs on Windows with an NVIDIA GPU. I used Windows x64, MSVC, Ninja, CUDA 13.3, TensorRT 10.16, OpenCV 4.12, Redis, PostgreSQL 17, Qt 6.11 (MinGW) and vcpkg. The other C++ libraries are listed in `vcpkg.json`.

The model file in `engines/` was built for my GPU and TensorRT version. On a different setup, export it again.

**1. Build the back end and the desktop app.** Change the paths to match your machine.

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

**2. Start it.** Start Redis and PostgreSQL first, then run:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\start_demo.ps1
```

The script asks for the camera address, an admin token and the database connection without showing them on screen, then opens the desktop app. In the app, click 检查服务 (check services), then 启动会话 (start session). The camera management page is at <http://127.0.0.1:8087/camera-admin>.

**3. Stop it, or run the tests.**

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\stop_demo.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\test_all.ps1
```

## How I built it

I started from a larger vision project and cut it down to one complete pipeline: one model (YOLO11n-pose), one kind of analysis (people flow) and a small desktop app. Then I added the shared video pipeline in small stages, finishing each one before starting the next.

1. **Fix the starting point.** Freeze the baseline and the design.
2. **One shared video reader.** Decode each camera once with FFmpeg and pass the frames to every job that needs them.
3. **Move people flow onto it.** The existing analysis became the first job using the shared reader.
4. **One worker, several roles.** A single worker process hosts the different jobs.
5. **Saving pictures.** Store task state, send commands through Redis, then add a job that saves JPEG frames and clears out old ones.
6. **A proper interface.** An HTTP API with token authentication and diagnostics, then a web page for managing cameras.
7. **Keeping it running.** Monitoring, recovery from failures, storage limits, and backup and restore.
8. **Stable camera IDs and PostgreSQL.** Each camera got a permanent ID, and the data moved from SQLite to PostgreSQL.

