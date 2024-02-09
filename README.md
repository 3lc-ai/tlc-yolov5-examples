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
| [https://github.com/3lc-ai/yolov5](YOLOv5)      | [https://github.com/3lc-ai/yolov5/tree/tlc_2.2.0/utils/loggers/tlc/README.md](YOLOv5 integration README.md)                |
| [https://github.com/3lc-ai/ultralytics](YOLOv8) | [https://github.com/3lc-ai/ultralytics/blob/tlc-integration/ultralytics/utils/tlc/README.md](YOLOv8 integration README.md) |

## Release Notes

We are making continuous improvements to the integrations, and aim to keep the integrations up-to-date with the upstream repositories and new features available in `tlc` itself.

The following table details the available releases for the YOLOv5/8 integration.

| Page                                    | Summary                                                                                                                                                                               |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [tlc_2.0.2](release_notes/tlc_2.0.2.md) | `--data` no longer required, optionally discard all classes but class 0, image embeddings, per-image loss metric, updated logging and error reporting and batched metrics collection. |
| [tlc_2.2.0](release_notes/tlc_2.2.0.md) | Overhaul of the 3LC integration, command line arguments replaced by environment variables, support for `tlc 2.2` project structure, 3LC YAML file, per-epoch and per-class metrics, and much more. Introduction of YOLOv8 integration.         |

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
