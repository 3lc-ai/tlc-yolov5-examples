# 8/12/2023 tlc_2.0.2 Release Notes

New features and improvements have been added to the [2.0.2
branch](https://github.com/3lc-ai/yolov5/tree/tlc_2.0.2) of the integration
fork. The following changes have been made:

**Remove need for --data argument**

The `--data` argument is no longer required to run training, validation, or
metrics collection. As before, the first time you run training, validation, or
metrics collection, the `--data` argument _should_ be specified, and this will
create an initial 3LC Table revision in the default location. As long as the
`--data` argument is supplied, subsequent runs will automatically use the latest
revision of the tables corresponding to these yaml files. However, it is now
possible to send the arguments `--tlc-train-revision-url` and
`--tlc-val-revision-url` (to train.py), or just `--tlc-revision-url` to (val.py
and collect.py) to specify a specific revision of the tables to use. Latest
revisions of the tables will not be used if these arguments are supplied.

**Add argument `--tlc-discard-non-zero-preds`** 

When adding the argument `--tlc-discard-non-zero-preds`, the validation scores
will be reported as if only class-0 predictions were made. This is useful for
cases where additional classes have been added to aid training, but are not of
interest for validation. This argument can be used with `train.py` and `val.py`.

**Add argument `--tlc-image-embeddings-dim` to enable per-sample embeddings
collection**

When adding the argument `--tlc-image-embeddings-dim=[2 or 3]` embeddings will
be collected for all the images in the dataset on every call to collect metrics.
The embeddings will be reduced using UMAP to the specified number of dimensions.
This argument can be used with `train.py` and `collect.py`. 

**Add argument `--tlc-mc-collect-loss` to enable per-sample loss collection**

When adding the argument `--tlc-mc-collect-loss` the losses will be collected
for all the images in the dataset on every call to collect metrics. This
argument can be used with `train.py` and `collect.py`.

**Updates to logging and error reporting**

Logging and error reporting has been overhauled and should now be more
informative and easier to understand.

**Batched metrics collection**

Metrics collection is now done following val.py - allowing for batched inference
with the `--batch-size` argument when using `collect.py`. From `train.py`,
metrics collection is done with twice the batch size used for training.