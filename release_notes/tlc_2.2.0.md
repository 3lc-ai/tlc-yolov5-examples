# 09/02/2024 tlc_2.2.0 Release Notes

A YOLOv8 object detection integration has been released, available as a [fork](https://github.com/3lc-ai/ultralytics/tree/tlc-integration) of the `ultralytics` repository! Documentation is available in a [README.md in the fork](https://github.com/3lc-ai/ultralytics/blob/tlc-integration/ultralytics/utils/tlc/README.md).

There are big improvements to the YOLOv5 integration, available on the [2.2.0 branch](https://github.com/3lc-ai/yolov5/tree/tlc_2.2.0). It corresponds to `tlc` version 2.2. This leads to some end user changes, which are detailed in this document.

## Project, tables and runs reorganization

The organization of projects, tables and runs has been changed in `tlc` v2.2.0. Corresponding changes have been made to the integration.

## `val.py --task collect` replaces `collect.py`

`collect.py` has been removed. In order to run metrics collection outside of training, call `val.py` and provide `--task collect`. This will collect metrics on the train and validation splits by default.

In order to collect metrics on a specific set of splits, the environment variable `TLC_COLLECTION_SPLITS` can be used by setting it to a comma-separated set of splits. All of these splits must be available in the YAML file provided through `--data`.

In order to collect embeddings and/or loss, refer to the corresponding environment variables for training.

## 3LC YAML file to specify tables to use

The previous way to specify which table revisions to use (`--data` empty and command line arguments) has been removed. A 3LC YAML file should now be used instead.

The 3LC YAML file should have (at minimum) keys `train` and `val`, which specify paths to the
tables to use for training and validation respectively. In order to train on a different revision, simply modify the corresponding URL in the YAML file. Aliases are respected.

An additional key `path` may be provided if the `train` and `val` have a common prefix. In this case, `path` is joined with the `train` and `val` paths.

On training for the first time on a regular YOLO dataset with the 3LC integration enabled, a 3LC dataset YAML file is written next to the YAML file used for that training run, which can be used going forward.

Pass the file just like any other YOLO dataset, except with a prefix `3LC://`, e.g. `--data 3LC://path/to/my/dataset.yaml`.

The paths in the 3LC YAML file can be any 3LC `Url`. Therefore, as an example, the tables and 3LC YAML file can reside on S3, for which something like `--data 3LC://s3://mybucket/dataset.yaml` can be used.

### Using the latest revision in a 3LC YAML

Once your 3LC YAML has `train` and `val` entries pointing to specific revisions, it will look like the following example:

```yaml
train: path/to/my_train_table/
val: path/to/my_val_table/
```

In order to train on different revisions, simply change the paths in the file to your desired revision. If you would like to train on the latest revisions in your lineages, you can add `:latest` to one or both of the paths, and 3LC will find the latest revision in the same lineage for those tables. For the above example, with latest on both, the 3LC YAML would look like this:

```yaml
train: path/to/my_train_table/:latest
val: path/to/my_val_table/:latest
```

An optional key `path` can be used for any common prefix to the Table Urls specified in the file, like this:

```yaml
path: path/to/
train: my_train_table/:latest
val: my_val_table/:latest
```

### Repeated calls with the original YAML

On repeatedly calling `train.py` with the same `--data path/to/some_file.yaml`, the 3LC tables created previously will be used again automatically. The file path name (i.e. `some_file` for `path/to/some_file.yaml`) is used for this, so if any tables have been created from this YAML name, they will be used. The latest revision will be used if new revisions are created for either the `train` or `val` table. The specific revisions used are logged to the console. A line is also printed stating that a 3LC YAML file has been created, with example usage.

---
**NOTE**: While it is possible to use the regular YAML, we recommend using a 3LC YAML to specify which revisions to use. Most importantly, it also enables using specific revisions of the dataset, and adding `:latest` in order to use the latest table in the lineage. After your first run, the written 3LC YAML file can be used.

---

## 3LC specific Command Line Arguments replaced by Environment Variables

3LC specific parameters (e.g. when to collect metrics, whether to collect embeddings etc.) are no longer
passed as command line arguments. Instead, use environment variables to specify such parameters. As
an example, instead of using `--tlc-image-embeddings-dim 2`, an environment variable `TLC_IMAGE_EMBEDDINGS_DIM=2`
should be used.

The following table details the changes. Script 'Both' refers to `train.py` and `val.py`.

| Command Line Argument (old)    | Environment Variable (new)          | Default | Comments                                                                          | Script     |
| ------------------------------ | ----------------------------------- | ------- | --------------------------------------------------------------------------------- | ---------- |
| `--tlc-disable-mc`             | `TLC_COLLECTION_DISABLE=true/false` | `false` |                                                                                   | `train.py` |
| `--tlc-mc-interval`            | `TLC_COLLECTION_EPOCH_INTERVAL`     | `1`     |                                                                                   | `train.py` |
| `--tlc-mc-start`               | `TLC_COLLECTION_EPOCH_START`        | `-1`    |                                                                                   | `train.py` |
| `--tlc-mc-before-training`     | N/A                                 |         | No longer possible to collect metrics before training.                            | N/A        |
| `--tlc-mc-iou-thres`           | N/A                                 |         | TP, FP and FN metrics are no longer computed, use the 3LC Dashboard instead.      | N/A        |
| `--tlc-mc-conf-thres`          | `TLC_CONF_THRES`                    | `0.1`   |                                                                                   | `train.py` |
| `--tlc-mc-nms-iou-thres`       | N/A                                 |         | No longer possible to set, YOLOv5 default of 0.6 is used.                         |            |
| `--tlc-mc-max-det`             | `TLC_MAX_DET`                       | `300`   |                                                                                   | `train.py` |
| `--tlc-mc-collect-loss`        | `TLC_COLLECT_LOSS`                  | `false` |                                                                                   | `train.py` |
| `--tlc-val-revision-url`       | N/A                                 |         | No longer an argument. Use a 3LC YAML file to specify revisions.                  | Both       |
| `--tlc-train-revision-url`     | N/A                                 |         | No longer an argument. Use a 3LC YAML file to specify revisions.                  | Both       |
| `--tlc-disable-sample-weights` | `TLC_SAMPLING_WEIGHTS=true/false`   | `false` |                                                                                   | `train.py` |
| `--tlc-discard-categories`     | N/A                                 |         | No longer possible as a command line option. Modify `val.py` yourself to discard. | N/A        |
| `--tlc-image-embeddings-dim`   | `TLC_IMAGE_EMBEDDINGS_DIM=0/2/3`    | `0`     |                                                                                   | Both       |

The following environment variable options are new:

| Environment Variable           | Default     | Comments                                                                      | Script                  |
| ------------------------------ | ----------- | ----------------------------------------------------------------------------- | ----------------------- |
| `TLC_COLLECTION_SPLITS`        | `train,val` | List of splits to collect metrics on.                                         | `val.py --task=collect` |
| `TLC_COLLECTION_VAL_ONLY`      | `false`     | If set to `true`, only collect metrics on the validation set during training. | `train.py`              |
| `TLC_IMAGE_EMBEDDINGS_REDUCER` | `umap`      | Which reducer to use for embeddings reduction.                                | Both                    |

Boolean environment variables can be supplied in a variety of ways: `yes/no`, `y/n`, `1/0` or `true/false`. These are not case sensitive, so e.g. `True` and `TRUE` also work.

A useful tip is to provide the environment variables as part of your call to `train.py` or `val.py --task=collect` like this (they only apply for the command they precede):

```bash
TLC_IMAGE_EMBEDDINGS_DIM=2 TLC_COLLECT_LOSS=true python train.py  --weights yolov5n.pt ...
```

### Examples

To make the transition as smooth as possible, here are some examples of how to go from a call with command line arguments to environment variables:

| Command Line Arguments                                                                                          | Environment Variables                                                                                 | Notes                                                                                                                                                                          |
| --------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `python train.py --data='' --tlc-image-embeddings-dim 2 --tlc-train-revision-url path/to/my_train_revision_url` | `TLC_IMAGE_EMBEDDINGS_DIM=2 python train.py --data 3LC://my_3lc_data.yaml`                            | Instead of specifying the Urls as command line arguments, notice how a 3LC YAML file used. It needs `train: path/to/my_train_revision_url` and a corresponding `val` revision. |
| `python train.py --data=coco128.yaml`                                                                           | `python train.py --data=coco128.yaml`                                                                 | For the first time, this will create 3LC Tables and a 3LC YAML pointing to those. On subsequent runs the latest revisions of these starting tables would be used.              |
| `python train.py --data=coco128.yaml --tlc-mc-start 0 --tlc-mc-interval 2`                                      | `TLC_COLLECTION_EPOCH_INTERVAL=2 TLC_COLLECTION_EPOCH_INTERVAL=0 python train.py --data=coco128.yaml` | Here no 3LC YAML is used, so on the first run the Tables are created, and for subsequent runs the latest table in the same lineage will be used.                               |
| `python collect.py --data='' --tlc-image-embeddings-dim 2 --tlc-revision-url path/to/my_revision_url`           | `TLC_IMAGE_EMBEDDINGS_DIM=2 python val.py --task collect --data 3LC://my_3lc_data.yaml`               | Here we also assume a 3LC YAML exists. Notice how `collect.py` is replaced with `val.py --task collect`.                                                                       |

## Metrics collection changes

Previously, metrics collection was only performed according to the user options (default every epoch) after training epochs and subsequent validation passes performed by YOLOv5.

### Single validation pass

Metrics (predictions and IoU) are now instead collected during the validation pass already performed by YOLOv5, and only an additional pass over the training split is performed, removing one pass over the validation set for each metrics collection.

### Defaults

Previously, the default metrics collection epoch settings were set to collect metrics for every epoch. In most cases this is far too often.

Metrics are now only collected after training (as a part of the after training validation pass). This collection pass always happens irrespective of `TLC_COLLECTION_EPOCH_INTERVAL` and `TLC_COLLECTION_EPOCH_START`. It is performed with the
model weights in `<yolo run dir>/weights/best.pt` after training has concluded (either through early stopping or reaching the final epoch). An additional column `Training Phase` is shown in the Dashboard for this pass which has the value `After Training`. In order to disable all metrics collection, including this pass, use `TLC_COLLECTION_DISABLE=true`.

`TLC_COLLECTION_EPOCH_START` now defaults to `-1`, which means that only this final `After training` pass is performed, and the value of `TLC_COLLECTION_EPOCH_INTERVAL` is ignored in this case.

The environment variables `TLC_COLLECTION_EPOCH_INTERVAL` and `TLC_COLLECTION_EPOCH_START` can be used to select when to collect metrics outside of the final pass described above. Importantly, just like validation passes, this uses the exponential moving average and not the `last.pt` or `best.pt` weights. These collection passes have regular integer epoch values in the Dashboard.

## Per-epoch metrics

In the Runs table for a project with YOLOv5 runs, various aggregate validation metrics are shown per-epoch, allowing you to follow your training runs as they are progressing and compare them with each other.

## Per-class metrics table

In addition to the per-sample metrics collected by 3LC, aggregate per-class metrics are also written at the end of calls to `train.py` or `val.py --task collect`. This output appears in a second metrics table, available in the 3LC dashboard.

## Use regular YOLOv5

You can now use YOLOv5 like before. Having `tlc` installed in your environment activates the integration.

Attempts to call `val.py` with `--task collect` or providing a `3LC://` prefixed path to `--data` (for either `train.py` or `val.py`) without `tlc` installed  will raise an error prompting you to install it.

## Discard predictions

When adding new `fake` object classes to your dataset, you could previously discard them with `--tlc-discard-categories` or `--tlc-discard-non-zero-preds`. This option is no longer available, but the behavior can still be attained by making changes to `val.py`. The following code can be added to `val.py` as a starting point, it removes predictions with category index 2, 7 and 19:

```python
discard_categories = [2, 7, 19] # Indices of classes to discard
for si, pred in enumerate(preds):
    # Filter out predictions with unwanted classes.
    # This is useful if you want to evaluate the model on a dataset where only some labels are relevant.
    mask = ~torch.isin(pred[:, 5], torch.tensor(discard_categories, device=pred.device))
    pred = pred[mask]
```
