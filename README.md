---
title: Roomscan
emoji: 🏠
colorFrom: green
colorTo: yellow
sdk: docker
app_port: 7860
python_version: "3.11"
suggested_hardware: cpu-basic
startup_duration_timeout: "1h"
---

## Metric Point Cloud Creation from Single Images using Depth Anything Model

This project leverages the Depth Anything model to create metric point clouds from single images. This repository includes all necessary scripts and files to perform depth estimation and generate accurate 3D point clouds with real-world measurements.

### Requirements

------------

To install the required packages, run:
`pip install -r requirements.txt`

### Installation

------------

1. Clone this repository:
`git clone https://github.com/bohdanvodianyk/image-to-pcd.git`
`cd your-repo-name`
2. Install the necessary Python packages:
`pip install -r requirements.txt`
3. Download the model checkpoints from [Google Drive](https://drive.google.com/drive/folders/1LJRnpOhNuzZXlVE0oGzzUb7ZiXeF6f-8?usp=sharing "Google Drive") and place them in the appropriate directory within the project.

### Usage

------------

#### Calibration
Before generating point clouds, calibrate your camera using the `calibration-camera.py` script. Ensure you have a chessboard pattern printed for the calibration process.

#### Depth Estimation to Point Cloud
To convert depth maps into metric point clouds, use the `depth_to_pointcloud.py` script. Ensure your input image is correctly formatted and accessible.

#### HEIC to PNG Conversion
If your input images are in HEIC format, convert them to PNG using the `heic2png.py` script.

#### Web UI
Start the local browser UI from the repository root:

```bash
python app.py
```

Open `http://127.0.0.1:5000`, select at least two JPG room photos, and follow the upload, point-cloud conversion, and registration steps. After registration, inspect the merged point cloud in the interactive home-page preview: drag to orbit and scroll to zoom. The merged point cloud and extracted floor-plan point cloud are also available to download. The UI requires the dependencies in `requirements.txt`, the camera calibration file, and the indoor model checkpoint.

#### Public Hugging Face Space
The root `Dockerfile` and `requirements-space.txt` are configured for a Docker Space. To deploy:

1. Create a **public Docker Space** on Hugging Face. Creating a compute-enabled Space requires a Hugging Face paid plan; the CPU Basic hardware itself has no hourly compute charge.
2. In a terminal at the project root, install and authenticate the Hugging Face CLI, then upload the source. Replace `<username>` and `<space-name>` with your Hugging Face account and new Space name:
   ```powershell
   python -m pip install --upgrade huggingface_hub
   hf auth login
   hf upload <username>/<space-name> . --repo-type space
   ```
   The project's `.gitignore` excludes local scans, model files, and generated outputs from the source upload. See Hugging Face's [Docker Space setup guide](https://huggingface.co/docs/hub/spaces-sdks-docker).
3. If its license and sharing terms allow, create a separate **private** Hugging Face model repository and upload the 1.34 GB checkpoint there:
   ```powershell
   hf upload <username>/<private-model-repo> checkpoints/depth_anything_metric_depth_indoor.pt depth_anything_metric_depth_indoor.pt --repo-type model
   ```
   Do not commit the checkpoint to this app repository.
4. In the Space's Settings, add `DEPTH_MODEL_REPO` as a variable containing the private model repository ID, and `HF_TOKEN` as a secret with read access to that model repository. The app downloads the checkpoint on first conversion. Alternatively, set `DEPTH_CHECKPOINT_PATH` to an available local checkpoint file.
5. Confirm the camera calibration file is present in the Space repo and wait for the Docker build to finish.

The public app has no user login: anyone who can access it can submit scans and use its CPU. Uploaded images and job results are written to the Space's temporary filesystem and may disappear when the Space restarts. Do not use it for sensitive images. For private scans or unrestricted use, use a private Space and add access control before sharing it.

### Model Checkpoints

------------

Model checkpoints necessary for depth estimation can be downloaded from the following Google Drive link:

[Google Drive - Model Checkpoints](https://drive.google.com/drive/folders/1LJRnpOhNuzZXlVE0oGzzUb7ZiXeF6f-8?usp=sharing "Google Drive - Model Checkpoints")

Download and place these checkpoints in the appropriate directory within your project to ensure the model functions correctly.

### Acknowledgements

------------

Special thanks to the developers of the **Depth Anything** model and all contributors who made this project possible. Your work in computer vision and deep learning is greatly appreciated.
