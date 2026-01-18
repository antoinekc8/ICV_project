# Traffic Analysis with YOLO Vehicle Detection

A computer vision project for vehicle detection, tracking, and traffic congestion analysis using YOLOv8.

## Project Overview

This project implements a complete pipeline for analyzing traffic videos:
- Vehicle detection using a custom-trained YOLOv8 model
- Multi-object tracking with centroid-based algorithm
- Real-time congestion level detection
- Vehicle counting by type (car, bus, van)
- Traffic metrics calculation (density, speed, flow rate)

## Features

- **Vehicle Detection**: Custom YOLOv8 model trained on DETRAC dataset
- **Object Tracking**: Centroid-based tracking to avoid double counting
- **Congestion Analysis**: Multi-level congestion detection (sparse, light, medium, heavy)
- **Traffic Metrics**: 
  - Vehicle density (vehicles/pixel)
  - Average speed (pixels/frame)
  - Flow rate (vehicles/minute)
  - Total vehicle count by type
- **Video Annotation**: Generates annotated videos with real-time overlay

## Dataset

- **Source**: UA-DETRAC (University at Albany DETRAC dataset)
- **Classes**: Car, Bus, Van
- **Training set**: DETRAC-Train-Annotations-XML
- **Test set**: DETRAC-Test-Annotations-XML
- **Images**: DETRAC-Images

## Project Structure

```
ICV_project/
├── data/                           # Raw DETRAC assets (images + XML)
│   ├── DETRAC-Images/
│   └── DETRAC-Train-Annotations-XML/ DETRAC-Test-Annotations-XML/
├── data_processed/                 # YOLO-ready splits and configs
│   ├── data.yaml
│   └── subset/
│       ├── data.yaml
│       ├── train/ images + labels
│       └── val/   images + labels
├── notebooks/                      # All experiment notebooks (preprocess, training, testing, demos)
│   ├── 0. Preprocessing.ipynb
│   ├── 1. Train.ipynb
│   ├── 2. Test.ipynb
│   ├── 3. Demo on test video.ipynb
├── results/                        # Reports, demo outputs, metrics
│   ├── traffic_analysis
│   ├── figures/
│   ├── metrics_analysis/
│   └── test_results/
├── runs/                           # Ultralytics run artifacts
│   ├── detect/
│   └── yolov8n_vehicle_detection*/
└── README.md 
```

## Installation

### Requirements

- Python 3.8+
- CUDA-capable GPU (recommended)

### Setup

1. Clone the repository:
```bash
git clone https://github.com/antoinekc8/ICV_project.git
cd ICV_project
```

2. Install dependencies:
```bash
pip install ultralytics opencv-python numpy pandas matplotlib tqdm
```

3. Download the DETRAC dataset and place it in the `data/` directory.

## Usage

### 1. Data Preprocessing

Run the preprocessing notebook to prepare the dataset:
```bash
jupyter notebook "notebooks/0. Preprocessing.ipynb"
```

This will:
- Parse XML annotations
- Convert to YOLO format
- Split data into train/val/test sets
- Generate `data.yaml` configuration

### 2. Model Training

Train the YOLOv8 model:
```bash
jupyter notebook "notebooks/1. Train and test.ipynb"
```

Key training parameters:
- Model: YOLOv8n (nano)
- Epochs: 50
- Image size: 640x640
- Batch size: 16
- Classes: car, bus, van

### 3. Traffic Analysis

Analyze traffic videos:
```bash
jupyter notebook "notebooks/3. Demo on test video.ipynb"
```

This notebook:
- Loads the trained weights from runs/
- Processes source videos frame-by-frame
- Tracks vehicles with unique IDs
- Calculates traffic metrics
- Saves annotated video and metrics to results/

### Demo Video Placement

You have to place the demo video (mp4 format) in data-processed/test after having run the first notebook (0. Preprocessing.ipynb). 

## Key Components

### CentroidTracker

Tracks vehicles across frames using centroid matching:
- Assigns unique IDs to vehicles
- Maintains vehicle history
- Handles disappearances and reappearances
- Prevents double counting

### CongestionDetector

Analyzes traffic flow and detects congestion:
- Monitors vehicle density
- Tracks average speed
- Calculates flow rates
- Classifies congestion levels based on configurable thresholds

## Metrics

The system calculates the following metrics:

- **Density**: Vehicles per pixel in the analysis zone
- **Speed**: Average vehicle movement in pixels per frame
- **Flow Rate**: Vehicles per minute
- **Congestion Level**: 
  - Sparse: Low density, normal speed
  - Light: Moderate density
  - Medium: High density with reduced speed
  - Heavy: Very high density with slow/stopped traffic

## Results

Results are saved in `results/traffic_analysis/`:
- `traffic_analysis_results.json`: Detailed metrics and statistics
- `analyzed_*.mp4`: Annotated output video with overlay

## Configuration

Adjust parameters in the notebooks:

**Model Training** (`1. Train and test.ipynb`):
- `epochs`: Number of training epochs
- `imgsz`: Input image size
- `batch`: Batch size

**Traffic Analysis** (`3. Demo2.ipynb`):
- `conf_threshold`: Detection confidence threshold (default: 0.25)
- `maxDisappeared`: Frames before removing tracked object (default: 30)
- `maxDistance`: Max pixel distance for centroid matching (default: 50)
- `road_length_px`: Analysis zone length in pixels (default: 1500)

## Performance

- **Detection Speed**: ~30 FPS on GPU
- **Model Size**: 6.2 MB (YOLOv8n)
- **Training Time**: ~2 hours on NVIDIA GPU
- **Inference**: Real-time capable

## Limitations

- Speed calculation is relative (pixels/frame) and requires calibration for real-world units
- Occlusions may affect tracking accuracy
- Model performance depends on lighting and camera angle
- Requires GPU for real-time processing

## Future Improvements

- Camera calibration for accurate speed measurement
- Deep SORT or ByteTrack for improved tracking
- Lane-specific analysis
- Traffic violation detection
- Multi-camera support
- Real-time streaming capability

## License

This project is part of an academic assignment at ENTPE (École Nationale des Travaux Publics de l'État).

## Authors

- Antoine KARILA-COHEN
- Alessandro RAMPOLDI
- Maxime DELPLANQUE
- Romain ARNAUD

## Acknowledgments

- UA-DETRAC dataset creators
- Ultralytics YOLOv8 team
- ENTPE Introduction to Computer Vision course