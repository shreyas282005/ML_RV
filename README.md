# Shape Detection, Measurement and Defect Classification

## 1. Project Overview

This project detects four geometric shapes using YOLOv8:
- Circle
- Square
- Triangle
- Rectangle

OpenCV is used to calculate the contour area and centroid of each detected shape. Based on the area, each shape is classified as Less, Moderate, or Severe.

The trained model is also exported to ONNX format.

## 2. Project Structure

Final_Shape_Detection_Project/
|
|-- dataset/
|   |-- images/
|   |   |-- train/
|   |   |-- val/
|   |   |-- test/
|   |-- labels/
|   |   |-- train/
|   |   |-- val/
|   |   |-- test/
|   |-- data.yaml
|
|-- models/
|   |-- best.pt
|   |-- best.onnx
|
|-- training/
|   |-- results.png
|   |-- results.csv
|   |-- args.yaml
|   |-- confusion_matrix.png
|   |-- confusion_matrix_normalized.png
|
|-- results/
|   |-- final_results.csv
|   |-- opencv_annotated/
|   |-- pytorch_predictions/
|   |-- onnx_predictions/

## 3. Dataset

Total images: 200

- Training: 140 images (70%)
- Validation: 40 images (20%)
- Testing: 20 images (10%)

Image resolution: 1920 x 1080

Classes: Circle, Square, Triangle and Rectangle.

## 4. Model Training

Model: YOLOv8n
Pretrained weights: yolov8n.pt
Input size: 640 x 640
Epochs: 100
Batch size: 16
Early stopping patience: 20
Hardware: NVIDIA Tesla T4 GPU

The best model is saved as models/best.pt.

## 5. Model Evaluation

### PyTorch

- Precision: 0.996
- Recall: 1.000
- mAP50: 0.995
- mAP50-95: 0.979
- Validation inference time: 6.4 ms/image

### ONNX

- Precision: 0.996
- Recall: 1.000
- mAP50: 0.995
- mAP50-95: 0.979
- Validation inference time: 13.7 ms/image

ONNX export settings:
- Image size: 640 x 640
- Opset: 12
- Dynamic input dimensions: Disabled
- Simplification: Enabled

The inference times are taken from their respective validation outputs, not from a separate controlled speed benchmark.

## 6. OpenCV Measurement

OpenCV calculates:
- Contour area in square pixels (px2)
- Centroid coordinates (x, y)
- Defect category based on area

### Defect Categories

| Area | Category |
|---|---|
| Less than 5000 px2 | Less |
| 5000 to 15000 px2 | Moderate |
| Greater than 15000 px2 | Severe |

## 7. Final Results

- Images processed: 200
- Total detections: 1075
- Objects measured: 1075
- Failed measurements: 0
- Fallback segmentation used: 18

The final CSV is available at results/final_results.csv.

## 8. Running the Model

Install the required Python packages:

    pip install ultralytics opencv-python pandas onnx onnxruntime

PyTorch inference example:

    from ultralytics import YOLO

    model = YOLO("models/best.pt")
    results = model.predict(
        source="dataset/images/test",
        imgsz=640,
        save=True
    )

ONNX inference example:

    from ultralytics import YOLO

    model = YOLO("models/best.onnx")
    results = model.predict(
        source="dataset/images/test",
        imgsz=640,
        save=True
    )

Note: GPU inference requires a compatible CUDA environment. CPU inference can also be used, although it may be slower.

## 9. Limitations

Complete ground-truth area and centroid measurements were not provided for the dataset. Therefore, area MAE, mean percentage error and centroid distance error could not be calculated reliably.

The reported detection metrics are based on the available dataset annotations. The contour measurements depend on the accuracy of object detection and segmentation.

## 10. Main Deliverables

- YOLO dataset and annotations
- Trained PyTorch model
- Exported ONNX model
- Training results and evaluation plots
- PyTorch and ONNX predictions
- OpenCV annotated images
- Final CSV containing object measurements and defect categories
