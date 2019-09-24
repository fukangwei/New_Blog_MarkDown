---
title: tensorpack函数总结
date: 2019-02-12 15:46:41
categories: 深度学习
---
### dataflow.TestDataSpeed

&emsp;&emsp;class `dataflow.TestDataSpeed(ds, size=5000, warmup=0)`: Test the speed of some `DataFlow`.

- `ds` (`DataFlow`): the `DataFlow` to test.
- `size` (`int`): number of datapoints to fetch.
- `warmup` (`int`): warmup iterations

&emsp;&emsp;Function:

- `get_data()`: Will run testing at the beginning, then produce data normally.
- `start()`: Alias of `start_test`.
- `start_test()`: Start testing with a progress bar.

### dataflow.MultiThreadMapData

&emsp;&emsp;class `dataflow.MultiThreadMapData(ds, nr_thread, map_func, buffer_size=200, strict=False)`: Same as `MapData`, but start threads to run the mapping function. This is useful when the mapping function is the bottleneck, but you don't want to start processes for the entire dataflow pipeline.
&emsp;&emsp;**Note**: There is tiny communication overhead with threads, but you should avoid starting many threads in your main process to reduce `GIL` contention.
&emsp;&emsp;The threads will only start in the process which calls `reset_state()`. Therefore you can use `PrefetchDataZMQ(MultiThreadMapData(...), 1)` to reduce `GIL` contention.
&emsp;&emsp;Threads run in parallel and can take different time to run the mapping function. Therefore the order of datapoints won't be preserved, and datapoints from one pass of `df.get_data()` might get mixed with datapoints from the next pass.
&emsp;&emsp;You can use strict mode, where `MultiThreadMapData.get_data()` is guaranteed to produce the exact set which `df.get_data()` produces. Although the order of data still isn't preserved.

&emsp;&emsp;Parameters:

- `ds` (`DataFlow`): the dataflow to map.
- `nr_thread` (`int`): number of threads to use.
- `map_func` (`callable`): `datapoint -> datapoint | None`.
- `buffer_size` (`int`): number of datapoints in the buffer.
- `strict` (`bool`): use `strict mode`.

### dataflow.MapDataComponent

&emsp;&emsp;class `dataflow.MapDataComponent(ds, func, index=0)`: Apply a `mapper/filter` on a datapoint component.
&emsp;&emsp;**Note**: This dataflow itself doesn't modify the datapoints. But please make sure func doesn't modify the components unless you're certain it's safe.
&emsp;&emsp;If you discard some datapoints, `ds.size()` will be incorrect.

- `ds` (`DataFlow`): input `DataFlow`.
- `func` (`TYPE -> TYPE | None`): takes `dp[index]`, returns a new value for `dp[index]`. return `None` to discard this datapoint.
- `index` (`int`): index of the component.

### dataflow.BatchData

&emsp;&emsp;class `dataflow.BatchData(ds, batch_size, remainder=False, use_list=False)`: Stack datapoints into batches. It produces datapoints of the same number of components as `ds`, but each component has one new extra dimension of size `batch_size`. The batch can be either a list of original components, or (by default) a numpy array of original components.

- `ds` (`DataFlow`): When `use_list=False`, the components of `ds` must be either scalars or `np.ndarray`, and have to be consistent in shapes.
- `batch_size` (`int`): batch size.
- `remainder` (`bool`): When the remaining datapoints in `ds` is not enough to form a batch, whether or not to also produce the remaining data as a smaller batch. If set to `False`, all produced datapoints are guaranteed to have the same batch size. If set to `True`, `ds.size()` must be accurate.
- `use_list` (`bool`): if `True`, each component will contain a list of datapoints instead of an numpy array of an extra dimension.

&emsp;&emsp;Function:

- `get_data()`: Yields: Batched data by stacking each component on an extra `0_th` dimension.

### dataflow.MapData

&emsp;&emsp;class `dataflow.MapData(ds, func)`: Apply a `mapper/filter` on the `DataFlow`.
&emsp;&emsp;**Note**: Please make sure `func` doesn't modify the components unless you're certain it's safe.
&emsp;&emsp;If you discard some datapoints, `ds.size()` will be incorrect.

- `ds` (`DataFlow`): input `DataFlow`.
- `func` (`datapoint -> datapoint | None`): takes a datapoint and returns a new datapoint. Return `None` to discard this datapoint.

### dataflow.PrefetchData

&emsp;&emsp;alias of `tensorpack.dataflow.parallel.MultiProcessPrefetchData`.

### dataflow.MultiProcessPrefetchData

&emsp;&emsp;class `dataflow.MultiProcessPrefetchData(ds, nr_prefetch, nr_proc)`: Prefetch data from a `DataFlow` using `Python` multiprocessing utilities. It will fork the process calling `__init__()`, collect datapoints from `ds` in each process by a `Python multiprocessing.Queue`.
&emsp;&emsp;**Note**: An iterator cannot run faster automatically, what's happening is that the underlying dataflow will be forked `nr_proc` times. As a result, we have the following guarantee on the dataflow correctness:
&emsp;&emsp;When `nr_proc = 1`, the dataflow produces the same data as `ds` in the same order.
&emsp;&emsp;When `nr_proc > 1`, the dataflow produces the same distribution of data as `ds` if each sample from `ds` is `i.i.d.` (e.g. fully shuffled). You probably only want to use it for training.
&emsp;&emsp;This has more serialization overhead than `PrefetchDataZMQ` when data is large.
&emsp;&emsp;You can nest like this: `PrefetchDataZMQ(PrefetchData(df, nr_proc = a), nr_proc = b)`. A total of a instances of df worker processes will be created.
&emsp;&emsp;fork happens in `__init__`. `reset_state()` is a `no-op`. The worker processes won't get called.

- `ds` (`DataFlow`): input `DataFlow`.
- `nr_prefetch` (`int`): size of the queue to hold prefetched datapoints.
- `nr_proc` (`int`): number of processes to use.

### dataflow.RNGDataFlow

&emsp;&emsp;class `dataflow.RNGDataFlow`: A `DataFlow` with `RNG`.
&emsp;&emsp;Function:

- `reset_state()`: Reset the `RNG`.

### dataflow.DataFlowTerminated

&emsp;&emsp;exception `dataflow.DataFlowTerminated`: An exception indicating that the `DataFlow` is unable to produce any more data, i.e. something wrong happened so that `calling get_data()` cannot give a valid iterator any more. In most `DataFlow` this will never be raised.

### dataflow.RemoteDataZMQ

&emsp;&emsp;class `dataflow.RemoteDataZMQ(addr1, addr2=None, hwm=50, bind=True)`: Produce data from `ZMQ PULL socket(s)`. It is the `receiver-side` counterpart of `send_dataflow_zmq()`, which uses `tensorpack.utils.serialize` for serialization. See `http://tensorpack.readthedocs.io/en/latest/tutorial/efficient-dataflow.html#distributed-dataflow`.

- `addr1`, `addr2` (`str`): addr of the `zmq` endpoint to connect to. Use both if you need two protocols (e.g. both `IPC` and `TCP`). I don't think you'll ever need `3`.
- `hwm` (`int`): `ZMQ` `high-water` mark (buffer size).
- `bind` (`bool`): whether to connect or bind the endpoint.

&emsp;&emsp;Function:

- `bind_or_connect(socket, addr)`

### dataflow.imgaug.RotationAndCropValid

&emsp;&emsp;class `dataflow.imgaug.RotationAndCropValid(max_deg, interp=cv2.INTER_LINEAR, step_deg=None)`: Random rotate and then crop the largest possible rectangle. Note that this will produce images of different shapes.
&emsp;&emsp;Function:

- `largest_rotated_rect(w, h, angle)`: Get largest rectangle after rotation.