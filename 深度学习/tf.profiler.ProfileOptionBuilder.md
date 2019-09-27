---
title: tf.profiler.ProfileOptionBuilder
date: 2019-02-13 00:23:29
categories: 深度学习
---
### Class ProfileOptionBuilder

&emsp;&emsp;Defined in `tensorflow/python/profiler/option_builder.py`. Option builder for profiling `API`. For tutorial on the options, see `https://github.com/tensorflow/tensorflow/tree/master/tensorflow/core/profiler/g3doc/options.md`.

``` python
# Users can use pre-built options:
opts = (tf.profiler.ProfileOptionBuilder.trainable_variables_parameter())
# Or, build your own options:
opts = (tf.profiler.ProfileOptionBuilder().with_max_depth(10).with_min_micros(1000) \
        .select(['accelerator_micros']).with_stdout_output().build())
# Or customize the pre-built options:
opts = (tf.profiler.ProfileOptionBuilder(tf.profiler.ProfileOptionBuilder.time_and_memory()) \
        .with_displaying_options(show_name_regexes=['.*rnn.*']).build())
# Finally, profiling with the options:
_ = tf.profiler.profile(tf.get_default_graph(), run_meta=run_meta, cmd='scope', options=opts)
```

### \_\_init\_\_

&emsp;&emsp;Constructor:

``` python
__init__(options=None)
```

- `options`: Optional initial option dict to start with.

### account_displayed_op_only

&emsp;&emsp;Whether only account the statistics of displayed profiler nodes:

``` python
account_displayed_op_only(is_true)
```

- `is_true`: If `true`, only account statistics of nodes eventually displayed by the outputs. Otherwise, a node's statistics are accounted by its parents as long as it's types match `account_type_regexes`, even if it is hidden from the output, say, by `hide_name_regexes`.

### build

&emsp;&emsp;Build a profiling option:

``` python
build()
```

Return a dict of profiling options.

### float_operation

&emsp;&emsp;Options used to profile float operations:

``` python
@staticmethod
float_operation()
```

Please see `https://github.com/tensorflow/tensorflow/tree/master/tensorflow/core/profiler/g3doc/profile_model_architecture.md` on the caveats of calculating `float` operations. Return a dict of profiling options.

### order_by

&emsp;&emsp;Order the displayed profiler nodes based on a attribute:

``` python
order_by(attribute)
```

- `attribute`: An attribute the profiler node has.

Supported attribute includes `micros`, `bytes`, `occurrence`, `params`, etc. See `https://github.com/tensorflow/tensorflow/tree/master/tensorflow/core/profiler/g3doc/options.md` for supported attributes.

### select

&emsp;&emsp;Select the attributes to display:

``` python
select(attributes)
```

- `attributes`: A list of attribute the profiler node has.

### time_and_memory

&emsp;&emsp;Show operation time and memory consumptions:

``` python
@staticmethod
time_and_memory(
        min_micros=1, min_bytes=1, min_accelerator_micros=0, min_cpu_micros=0,
        min_peak_bytes=0, min_residual_bytes=0, min_output_bytes=0)
```

- `min_micros`: Only show profiler nodes with execution time no less than this. It sums accelerator and cpu times.
- `min_bytes`: Only show profiler nodes requested to allocate no less bytes than this.
- `min_accelerator_micros`: Only show profiler nodes spend no less than this time on accelerator (e.g. `GPU`).
- `min_cpu_micros`: Only show profiler nodes spend no less than this time on cpu.
- `min_peak_bytes`: Only show profiler nodes using no less than this bytes at peak (high watermark). For profiler nodes consist of multiple graph nodes, it sums the graph nodes' `peak_bytes`.
- `min_residual_bytes`: Only show profiler nodes have no less than this bytes not being `de-allocated` after `Compute()` ends. For profiler nodes consist of multiple graph nodes, it sums the graph nodes' `residual_bytes`.
- `min_output_bytes`: Only show profiler nodes have no less than this bytes output. The output are not necessarily allocated by this profiler nodes.

Return a dict of profiling options.

### trainable_variables_parameter

&emsp;&emsp;Options used to profile trainable variable parameters:

``` python
@staticmethod
trainable_variables_parameter()
```

Normally used together with `scope` view. Return a dict of profiling options.

### with_accounted_types

&emsp;&emsp;Selectively counting statistics based on node types:

``` python
with_accounted_types(account_type_regexes)
```

Here, `types` means the profiler nodes' properties. Profiler by default consider device name (e.g. `/job:xx/.../device:GPU:0`) and operation type (e.g. `MatMul`) as profiler nodes' properties. User can also associate customized `types` to profiler nodes through `OpLogProto` proto.
&emsp;&emsp;For example, user can select profiler nodes placed on `gpu:0` with `account_type_regexes=['.*gpu:0.*']`
&emsp;&emsp;If none of a node's properties match the specified regexes, the node is not displayed nor accounted.

- `account_type_regexes`: A list of regexes specifying the types.

### with_empty_output

&emsp;&emsp;Do not generate `side-effect` outputs:

``` python
with_empty_output()
```

### with_file_output

&emsp;&emsp;Print the result to a file:

``` python
with_file_output(outfile)
```

### with_max_depth

&emsp;&emsp;Set the maximum depth of display:

``` python
with_max_depth(max_depth)
```

The depth depends on profiling view. For `scope` view, it's the depth of name scope hierarchy (`tree`), for `op` view, it's the number of operation types (`list`), etc.

- `max_depth`: Maximum depth of the data structure to display.

### with_min_execution_time

&emsp;&emsp;Only show profiler nodes consuming no less than `min_micros`:

``` python
with_min_execution_time(min_micros=0, min_accelerator_micros=0, min_cpu_micros=0)
```

- `min_micros`: Only show profiler nodes with execution time no less than this. It sums accelerator and cpu times.
- `min_accelerator_micros`: Only show profiler nodes spend no less than this time on accelerator (e.g. `GPU`).
- `min_cpu_micros`: Only show profiler nodes spend no less than this time on cpu.

### with_min_float_operations

&emsp;&emsp;Only show profiler nodes consuming no less than `min_float_ops`:

``` python
with_min_float_operations(min_float_ops)
```

Please see `https://github.com/tensorflow/tensorflow/tree/master/tensorflow/core/profiler/g3doc/profile_model_architecture.md` on the caveats of calculating float operations.

- `min_float_ops`: Only show profiler nodes with float operations no less than this.

### with_min_memory

&emsp;&emsp;Only show profiler nodes consuming no less than `min_bytes`:

``` python
with_min_memory(min_bytes=0, min_peak_bytes=0, min_residual_bytes=0, min_output_bytes=0)
```

- `min_bytes`: Only show profiler nodes requested to allocate no less bytes than this.
- `min_peak_bytes`: Only show profiler nodes using no less than this bytes at peak (high watermark). For profiler nodes consist of multiple graph nodes, it sums the graph nodes' `peak_bytes`.
- `min_residual_bytes`: Only show profiler nodes have no less than this bytes not being `de-allocated` after `Compute()` ends. For profiler nodes consist of multiple graph nodes, it sums the graph nodes' `residual_bytes`.
- `min_output_bytes`: Only show profiler nodes have no less than this bytes output. The output are not necessarily allocated by this profiler nodes.

### with_min_occurrence

&emsp;&emsp;Only show profiler nodes including no less than `min_occurrence` graph nodes:

``` python
with_min_occurrence(min_occurrence)
```

A `node` means a profiler output node, which can be a python line (code view), an operation type (op view), or a graph node (`graph/scope` view). A python line includes all graph nodes created by that line, while an operation type includes all graph nodes of that type.

- `min_occurrence`: Only show nodes including no less than this.

### with_min_parameters

&emsp;&emsp;Only show profiler nodes holding no less than `min_params` parameters:

``` python
with_min_parameters(min_params)
```

`Parameters` normally refers the weights of in `TensorFlow` variables. It reflects the `capacity` of models.

- `min_params`: Only show profiler nodes holding number parameters no less than this.

### with_node_names

&emsp;&emsp;Regular expressions used to select profiler nodes to display:

``` python
with_node_names(start_name_regexes=None, show_name_regexes=None, hide_name_regexes=None, trim_name_regexes=None)
```

After `with_accounted_types` is evaluated, `with_node_names` are evaluated as follows:
&emsp;&emsp;For a profile data structure, profiler first finds the profiler nodes matching `start_name_regexes`, and starts displaying profiler nodes from there. Then, if a node matches `show_name_regexes` and doesn't match `hide_name_regexes`, it's displayed. If a node matches `trim_name_regexes`, profiler stops further searching that branch.

- `start_name_regexes`: list of node name regexes to start displaying.
- `show_name_regexes`: list of node names regexes to display.
- `hide_name_regexes`: list of node_names regexes that should be hidden.
- `trim_name_regexes`: list of node name regexes from where to stop.

### with_pprof_output

&emsp;&emsp;Generate a pprof profile `gzip` file:

``` python
with_pprof_output(pprof_file)
```

To use the pprof file: `pprof -png --nodecount=100 --sample_index=1`.

- `pprof_file`: filename for output, usually suffixed with `.pb.gz`.

### with_stdout_output

&emsp;&emsp;Print the result to `stdout`:

``` python
with_stdout_output()
```

### with_step

&emsp;&emsp;Which profile step to use for profiling:

``` python
with_step(step)
```

The `step` here refers to the step defined by `Profiler.add_step() API`.

- `step`: When multiple steps of profiles are available, select which step's profile to use. If `-1`, use average of all available steps.

### with_timeline_output

&emsp;&emsp;Generate a timeline json file：

``` python
with_timeline_output(timeline_file)
```

---

### How to calculate a net's FLOPs in CNN

&emsp;&emsp;Problem: I want to design a convolutional neural network which occupy `GPU` resource no more than `Alexnet`. I want to use `FLOPs` to measure it but I don't know how to calculate it. Is there any tools to do it, please?
&emsp;&emsp;Answer: For future visitors, if you use `Keras` and `TensorFlow` as Backend, then you can try the following example. It calculates the `FLOPs` for the `MobileNet`:

``` python
import tensorflow as tf
import keras.backend as K
from keras.applications.mobilenet import MobileNet
​
run_meta = tf.RunMetadata()

with tf.Session(graph=tf.Graph()) as sess:
    K.set_session(sess)
    net = MobileNet(alpha=.75, input_tensor=tf.placeholder('float32', shape=(1, 32, 32, 3)))
    opts = tf.profiler.ProfileOptionBuilder.float_operation()
    flops = tf.profiler.profile(sess.graph, run_meta=run_meta, cmd='op', options=opts)
    opts = tf.profiler.ProfileOptionBuilder.trainable_variables_parameter()
    params = tf.profiler.profile(sess.graph, run_meta=run_meta, cmd='op', options=opts)
    print("{:,} --- {:,}".format(flops.total_float_ops, params.total_parameters))
```