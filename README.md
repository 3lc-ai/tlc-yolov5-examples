# 3LC integrations with YOLOv5 and YOLOv8

[3LC](https://3lc.ai) is a tool which gives real-time insights into how
machine-learning models learn from data, enabling data scientists to make
informed decisions, enhancing model accuracy and performance. This repository
contains documentation and instruction for how to use 3LC with the
[YOLOv5](https://github.com/ultralytics/yolov5) "You-Only-Look-Once" object
detection model.

In order to use 3LC and YOLOv5/8 together, integration forks of `YOLOv5` and `ultraltyics` have been
created that incorporate 3LC metrics collection into the existing frameworks.

The integration forks are designed to be drop-in replacements for the official
repositories, and adds extra 3LC options to customize various 3LC features.

## License Disclaimer

This repository contains examples of YOLOv5 and YOLOv8 (`ultralytics`) usage and is distributed under a
Dual License. The portions of the code specific to YOLOv5 and YOLOv8 are made available
under the AGPL-3.0 license, following the license terms of YOLOv5 and YOLOv8, which is
maintained by Ultralytics. A fork with YOLOv5 integration is also available
under the AGPL-3.0 license.

The remaining parts of the code in this repository are distributed under a
permissive BSD license.

Enterprise usage is permitted under this Dual License setup; however, it is
subject to the commercial license terms of Ultralytics YOLOv5 and YOLOv8.

### Licensing Files

- For code specific to YOLOv5, YOLOv8 and the corresponding integration forks, refer to [LICENSE-AGPL-3.0](./LICENSE-AGPL-3.0).
- For all other code, refer to [LICENSE-BSD](./LICENSE-BSD).

## Getting Started and Documentation

In order to get started using the integrations and see documentation, refer to the `README.md` files in the respective forks:

| Integration                                     | Getting Started / Documentation                                                                                            |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| [YOLOv5](https://github.com/3lc-ai/yolov5)      | [YOLOv5 Integration README.md](https://github.com/3lc-ai/yolov5/blob/tlc_2.5/utils/loggers/tlc/README.md)                  |
| [YOLOv8](https://github.com/3lc-ai/ultralytics) | [YOLOv8 Integration README.md](https://github.com/3lc-ai/ultralytics/blob/tlc-integration/ultralytics/utils/tlc/README.md) |

## Release Notes

We are making continuous improvements to the integrations, and aim to keep the integrations up-to-date with the upstream repositories and new features available in `3lc` itself.

For YOLOv5, there are different branches corresponding to several different `3lc` versions. The latest supported version is kept as the default branch of the repository.

For YOLOv8 (in the repository named `ultralytics`), there is a single branch `tlc-integration` which is kept up to date with the latest major version of `3lc`. No branches are kept for older versions.

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
