# People-Flow Analysis from Live Camera Video

By Zichun Ai, MSc Internet of Things with AI, University of Bristol

This system watches a live camera feed, finds and follows people, counts how many cross a line, and raises an alert when someone enters a restricted area or shows a risky pose such as a fall.

It is a smaller, standalone version of the kind of system I worked on during my machine-vision internship (May–Aug 2026).

## What it can do

- **Find people**, along with 17 body points such as shoulders, knees and ankles
- **Follow each person** with a stable ID and draw their path
- **Count** who crosses a line, and in which direction
- **Watch an area** and notice when someone enters, stays or leaves
- **Spot simple poses**: hands up, crouching, falling, running and loitering
- **Show the results** in a desktop app, with a small web page for managing cameras

## What I like about it

One camera feed often has to serve several jobs at once, such as running the AI model and saving pictures. Here the video is decoded only once and shared by all of them. If one job is slow or fails, the others keep going.

- It has been run end to end on a real 1080p camera.
- Automated tests cover situations like a job failing, a slow job, or the video dropping and coming back.
- Camera passwords stay out of the config files and code, and a scan of the program's logs found none.

## Good to know

This is a learning and demo project. I haven't yet measured how accurate the tracking and counting are on labelled video.

## Built with

C++ · CUDA · TensorRT · YOLO11-pose · FFmpeg · OpenCV · Redis · PostgreSQL · Qt

The YOLO layer for TensorRT comes from [tensorrtx](https://github.com/wang-xinyu/tensorrtx) (MIT License).

Build steps and what I learned along the way are in [BUILD_NOTES.md](BUILD_NOTES.md).

## My other projects

- [vehicle-type-colour-recognition](https://github.com/guagua-paopao/vehicle-type-colour-recognition): recognises vehicle type and colour from camera video
- [document-qa-assistant](https://github.com/guagua-paopao/document-qa-assistant): an AI assistant that answers questions from company documents
