# 3LC and YoloV5 integration

[3LC](https://3lc.ai) is a tool which gives real-time insights into how
machine-learning models learn from data, enabling data scientists to
make informed decisions, enhancing model accuracy and performance. This
repository contains documentation and instruction for how to use 3LC with the
[YOLOv5](https://github.com/ultralytics/yolov5) "You-Only-Look-Once" object
detection model.

In order to use 3LC and YOLOv5 together, an integration fork of 
YOLOv5 has been created that incorporates 3LC metrics collection into the 
existing YOLOv5 framework. 

The integration fork is available on [GitHub](https://github.com/3lc-ai/yolov5)
and it can be cloned using the following command:

```sh
git clone https://github.com/3lc-ai/yolov5
```

The integration fork is designed to be a drop-in replacement for the official
YOLOv5 repository, and adds some extra command-line options to `train.py` 
to control 3LC metrics collection.

# License Disclaimer

This repository contains examples of YOLOv5 usage and is distributed under a
Dual License. The portions of the code specific to YOLOv5 are made available
under the AGPL-3.0 license, following the license terms of YOLOv5, which is
maintained by Ultralytics. A fork with YOLOv5 integration is also available
under the AGPL-3.0 license.

The remaining parts of the code in this repository are distributed under a
permissive BSD license.

Enterprise usage is permitted under this Dual License setup; however, it is
subject to the commercial license terms of Ultralytics YOLOv5.

## Licensing Files
- For code specific to YOLOv5 and the integration fork, refer to
  [LICENSE-AGPL-3.0](./LICENSE-AGPL-3.0).
- For all other code, refer to [LICENSE-BSD](./LICENSE-BSD).

# Examples
For getting started quickly, refer to the `examples` directory. It features an
example of training and metrics collection on the `COCO128` dataset, and an
example of exporting data 

# Usage

## Setup
To get set up, clone the integration fork as described above, install its
requirements with `pip install -r requirements.txt` and finally install the
`tlc`-Python package.

## Training

Datasets are automatically registered with 3LC when starting training. To get
started, simply call `train.py` just like you would previously.

```python
python train.py --data coco128.yaml --weights yolov5n.pt --device 0
```

In order to customize 3LC specific parameters, use the various 3LC specific
command line arguments which have been added. Use `python train.py -h` and look
for the parameters with names starting with `tlc`.

They will allow you to specify if, when, and how often, to collect metrics
during training, and which revisions to use for validation and training. It is
also possible to disable metrics collection entirely for a training run. 

## Collecting metrics
There are two ways to perform 3LC metrics collection in YOLOv5. They can either
be captured during training, or separate from training.

Note that in both cases, metrics collection uses a batch size of 1 in order to
closely resemble inference passes as can be done in `detect.py`, which has the
same batch size limitation.

### During training
Metrics are automatically captured after each epoch during training. You can
customize which epochs to collect metrics for during training by providing a set
of different command line arguments.

### Outside of training
It is also possible to collect metrics independent of training. The integration
fork has a script `collect.py`, analogous to `detect.py` with labels, which
collects 3LC metrics for the provided data and model. This is useful for cases
where you already have a trained model and would like to start collecting
metrics without doing any more training, or e.g. for cases where you would like
to collect metrics on different image sizes from those used in training.

```python
python collect.py --data coco128.yaml --weights yolov5n.pt --device 0
```

## Exporting data
In order to export your bounding box data back into the YOLO format, `export.py`
can be used. It accepts three command line arguments, where you specify the path
to the table to export data from, the path to where you want your labels to end
up, and optionally whether to overwrite any existing labels at that location.
Use `python export.py -h` for more details.

Note that `export.py` only exports the labels and a draft dataset YAML file
(without a path to the images, you will have to fill in the blanks depending on
where you have your images and labels). You have to move your labels next to
your images to use them for training, the script will not copy the images for
you.

To export the labels to a different format we recommend to start with
`export.py` and convert it to suit your needs.