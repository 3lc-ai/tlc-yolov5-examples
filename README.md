# Disclaimer
Dual license...

Requires using fork which is under AGPL-license, original code distributed by Ultralytics...

# Introduction
The central workflow when using 3LC is repeated model training, metrics collection and data editing. This document takes you through how to do this in YOLOv5. 

# Examples
For getting started quickly, refer to the `/examples` directory.

# Usage

## Training

Datasets are automatically registered with 3LC when starting training. To get started, simply call `train.py` just like you would previously.
```python
python train.py ...
```

In order to customize 3LC specific parameters, use the various 3LC specific command line arguments which have been added. Use `python train.py -h` and look for the parameters with names starting with `tlc`. They will allow you to specify if, when and how often to collect metrics during training, which revisions to use for validation and training and more.

## Collecting metrics
There are two ways to perform 3LC metrics collection in YOLOv5. They can either be captured during training, or separate from training.

### During training
Metrics are automatically captured after each epoch during training. You can customize which epochs to collect metrics for during training by providing a set of different command line arguments.

### Outside of training
It is also possible to collect metrics outside of training. The integration fork has a script `collect.py`, analogous to `detect.py` with labels, which collects 3LC metrics for the provided data and model. This is useful for cases where you already have a trained model and would like to start collecting metrics without doing any more training.

Recommended settings:

## Exporting data
In order to export your bounding box data back into the YOLO format, `export.py` can be used. It accepts three command line arguments, where you specify the path to the table to export data from, the path to where you want your labels to end up, and optionally whether to overwrite any existing labels at that location. Use `python exporter.py -h` for more details.

Note that `export.py` only exports the labels. You have to move your labels next to your images to use them for training, the script will not copy the images for you.

To export the labels to a different format we recommend to start with `export.py` and convert it to suit your needs.


# IMPROVEMENTS TO YOLOV5 INTEGRATION
Timeout parameter
Make running training with 3lc depend on whether tlc is installed or not. Use default dataloaders and so on if not and warn about it.
project_path, needed?
tablefromyolo improvements