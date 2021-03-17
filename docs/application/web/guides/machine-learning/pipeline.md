# Pipeline
Pipeline API enables use of [NNStreamer](https://nnstreamer.ai/) machine learning inference pipelines in your applications. You can use them to process data with machine learning models and custom callbacks

## Main features of Pipeline API

The main features of the Pipeline API include:

- Create machine learning inference pipelines

  You can [set up pipelines](#create-machine-learning-inference-pipelines)

- Run machine learning inference pipelines

  You can [start and stop data flow within pipeline](#run-machine-learning-inference-pipelines)

- Dispose machine learning inference pipelines

  You can [dispose pipelines when they are no longer needed](#dispose-machine-learning-inference-pipelines)

- Get and set pipeline node properties

  You can [get and change pipeline nodes' settings](#get-and-set-pipeline-node-properties)

- Input data from application

  You can [use Source API to input data from JS application](#input-data-from-application)

- Read data from pipeline output

  You can [use SinkListener API to get pipeline output](#read-data-from-pipeline-output)

- Change data flow within pipeline

  You can [use Switch API to choose the pipeline branch that should receive data](#change-data-flow-within-pipeline)

- Start and stop data flow to a pipeline branch

  You can [use Valve API to stop and resume data flow within a pipeline branch](#start-and-stop-data-flow-to-a-pipeline-branch)

- Write custom data filters

  You can [use CustomFilter API to write custom data processing routines in JS](#write-custom-data-filters)

## Prerequisites

To use the Pipeline (in [mobile](../../api/latest/device_api/mobile/tizen/ml_pipeline.html), [wearable](../../api/latest/device_api/wearable/tizen/ml_pipeline.html), and [tv](../../api/latest/device_api/tv/tizen/ml_pipeline.html) ml_pipelines) API, the application has to request permission by adding the following privileges to the `config.xml` file:

```xml
<!-- for loading machine learning models from internal storage only -->
<tizen:privilege name="http://tizen.org/privilege/mediastorage"/>
<!-- for loading machine learning models from external storage only -->
<tizen:privilege name="http://tizen.org/privilege/externalstorage"/>
```

Additionally, to access files using the MLSingleShot API (in [mobile](../../api/latest/device_api/mobile/tizen/ml_single.html) and [wearable](../../api/latest/device_api/wearable/tizen/ml_single.html) applications), the application has to request [proper storage permissions](../security/privacy-related-permissions.md) using the PPM API (in [mobile](../../api/latest/device_api/mobile/tizen/ppm.html) and [wearable](../../api/latest/device_api/wearable/tizen/ppm.html) applications).

## Create machine learning inference pipelines

NNStreamer is a plugin for [GStreamer](https://gstreamer.freedesktop.org), adding tensor data types and enabling use of machine learning inference models in pipelines.
You can use some of the standard GStreamer and NNStreamer elements your applications.

NNStreamer pipelines are created from description like:
```
"appsrc ! other/tensor,dimension=(string)2:1:1:1,type=(string)int8,framerate=(fraction)0/1 ! tensor_filter framework=custom-easy model=my-custom-filter ! tensor_sink"
```
Exclamation marks `!` separate pipeline nodes.
You can learn how to define pipeline descriptions from [GStreamer documentation](#https://gstreamer.freedesktop.org/documentation).

To create a machine learning inference pipeline:

1. Describe it as a string:
   ```
   var pipelineDescription = "videotestsrc num-buffers=3 " +
                             "! video/x-raw,width=20,height=15,format=BGRA " +
                             "! tensor_converter " +
                             "! tensor_filter framework=custom-easy model=flattenFilter " +
                             "! fakesink";
   ```
2. Optionally, define a `PipelineStateChangeListener`, which will be called when pipeline changes its state:
   ```
   function pipelineStateChangeListener(newState) {
     console.log('New pipeline state: ' + newState);
   }
   ```

3. Call `tizen.ml.pipeline.createPipeline()` method:
   ```
   var pipeline = tizen.ml.pipeline.createPipeline(pipelineDescription, pipelineStateChangeListener);
   ```

Now you can use `pipeline` object to manage the pipeline.

## Run machine learning inference pipelines
Newly created pipeline transitions through `READY` to `PAUSED` state - you have to manually start it to set it in `PLAYING` state.

To start data flow within a pipeline:
1. Call its `start()` method:
   ```
   pipeline.start();
   ```

2. When inference will start, the `PipelineStateChangeListener` will be called with information about the new state.
You can also check the current pipeline state manually, by checking the value of its `state` property.

   ```
   console.log(pipeline.state); // 'PLAYING'
   ```

3. To stop inference, call pipeline's `stop()` method:
   ```
   pipeline.stop();
   ```

When pipeline stops, it changes its state to `PAUSED`.

## Dispose machine learning inference pipelines
Machine learning pipelines can be expensive in terms of used resources like memory.
You can dispose pipelines to reclaim resources.

To dispose a pipeline, call its `dispose()` method:
  ```
  pipeline.dispose();
  ```

Disposed pipelines enter `NULL` state.
They cannot be restarted and an attempt of calling any method of a disposed pipeline results in `NotFoundError` exception.

## Related Information
* Dependencies
  - Tizen 6.5 and Higher for Mobile
  - Tizen 6.5 and Higher for Wearable
  - Tizen 6.5 and Higher for TV
