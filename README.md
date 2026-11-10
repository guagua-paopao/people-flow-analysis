# People-Flow Analysis from Live Camera Video

By Zichun Ai, MSc Internet of Things with AI, University of Bristol

You point this at a live camera and it finds people, follows them, counts how many cross a line, and warns you if someone walks into a restricted area or does something risky like falling over.

It's a smaller, standalone version of the kind of system I worked on during my machine-vision internship (May–Aug 2026).

## What it can do

- It finds people and 17 points on their body, like shoulders, knees and ankles.
- It keeps the same ID on each person and draws the path they walked.
- It counts who crosses a line and which way they went.
- It can watch an area and notice when someone goes in, stays or leaves.
- It picks up simple poses like hands up, crouching, falling, running and loitering.
- You can see the results in a desktop app, and there's a small web page for managing cameras.

## What I like about it

One camera feed often has to do several jobs at once, like running the AI model and saving pictures, so I decode the video once and let all the jobs share it. If one job gets slow or fails, the others just carry on.

- I've run the whole thing on a real 1080p camera.
- The automated tests cover things like a job failing, a job running slow, and the video dropping and coming back.
- Camera passwords never go in the config files or code, and when I scanned the program's logs there were none in there either.

## Good to know

This is a learning and demo project, and I haven't measured how accurate the tracking and counting are on labelled video yet.

## Built with

C++ · CUDA · TensorRT · YOLO11-pose · FFmpeg · OpenCV · Redis · PostgreSQL · Qt

The YOLO layer for TensorRT comes from [tensorrtx](https://github.com/wang-xinyu/tensorrtx) (MIT License).

Build steps are in [BUILD_NOTES.md](BUILD_NOTES.md).

## My other projects

- [vehicle-type-colour-recognition](https://github.com/guagua-paopao/vehicle-type-colour-recognition): recognises vehicle type and colour from camera video
- [document-qa-assistant](https://github.com/guagua-paopao/document-qa-assistant): an AI assistant that answers questions from company documents
