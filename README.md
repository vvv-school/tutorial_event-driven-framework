# An introduction to the event-driven framework
We introduce the following topics to understand how the event-driven framework works:

### Requirements:
_Note: if you are using the virtual machine this already done_

In addition to YARP and iCubContrib you will need the **robotology/event-driven** (https://github.com/robotology/event-driven.git) library. You will also need the event-driven-data available [here](https://figshare.com/s/0abd8f18312bec15b121). You can automatically get the datasets using:

```
$ cd $ROBOT_CODE
$ mkdir datasets && cd datasets
$ wget -O vvv18-eventdriven-dataset.tar.gz https://ndownloader.figshare.com/files/10322340
```

if you haven't already done so.

## The Neuromorphic iCub
The purple iCub comes equipped with neuromorphic hardware.
- Within each eye is an Asynchronous Time-based Image Sensor (ATIS) event-driven camera.
- The lens is shared with a high resolution frame-based camera (Python) using a beam-splitter to expose both sensors to the same light. Both cameras "see" the same scene.
- The frame-based cameras are connected to the _icub-head_ computer.
- The event-based cameras are connected to the specialised _icub-zynq_ computer. This chip contains both an FPGA and an ARM CPU that are interfaced. The FPGA connects to the ATIS sensors and the CPU (running Linux) is connected on the iCub's network.

## Event review
- An event is something that happens at a precise time. The ATIS produces an event at a pixel location [u, v] given the light falling on the pixel changes (higher or lower) [p]. The event is also addressed to the left or right camera [c].
- See [this](https://www.youtube.com/watch?v=kPCZESVfHoQ) video for a good overview of the operation of dynamic vision sensors.
- Event cameras have: low-power, low-latency, high temporal-resolution, no frame-rate limitiations, and a compressed visual signal.
- We want to use them towards developing fast robots with long-term autonomy.

## Event-driven Framework with YARP
Event-driven data is integrated in YARP so we can do distributed event-driven sensing and processing and integrate with standard iCub sensors as well as behaviour and control modules. The *zynqGrabber* is a YARP module that runs on the _icub-zynq_; it exposes the data from the event-driven cameras on a YARP port. Data is sent in packets of several thousand events wrapped in a ``yarp::os::Bottle`` which we have called an ``ev::vBottle``. Processing modules read packets of events asynchronously using callback functions on the input ports - only when data arrives is processing performed. 

Click: for the [documentation](http://robotology.github.io/event-driven/doxygen/doc/html/index.html) for the event-driven libraries.
Click: for the [main project page](https://github.com/robotology/event-driven).

### Modules: vFramer
Unlike standard cameras, there are no "images" or "frames" when using events so to visualise the camera output on a synchronous display we need to create an image frame from recent events. This means that we are going to grab all of the events within a given time window (e.g. 30ms) and create a frame. The vFramer module does this for us. 

There are several command line arguments you can use in the vFramer, here is an example:

```javascript
--frameRate 30 --displays "(0 /left (AE ISO) 1 /right (AE ISO))" --height 240 --width 304
```
If possible vFramer will try to render frames at the given --frameRate, and the camera resolution can be set in --height and --width. The --displays argument defines how the vFramer will draw frames using the format [channel#, outportname, drawtypes] with as many tuples defined as you like. The drawtypes can also be chained such that we can draw multiple different event types layered on the same image. Order is important and some draw types are incompatible.

### Modules: vPreProcess

Pre-processing is performed on the event-stream to perform functions and filtering that is common across many modules. Available operations are:
- mirror or flip the image
- remove camera lens distortion
- check event consistency (is the data valid?)
- split a stereo stream into separate streams for each channel
- apply salt and pepper filtering

Standard options for VVV18 will be:
```javascript
--flipx --flipy --precheck false --pepper --temporalSize 250000 --undistort false
```

## Tutorial
In this tutorial we are going to:
- Introduce the dataset we will use in assignemnts.
- Introduce the typical data flow through an event-driven application.
- See what options are available for the ``vPreProcess`` module.
- See what options are available for visualisation with the ``vFramer`` module.

![Application Layout](https://github.com/vvv-school/tutorial_event-driven-framework/blob/master/misc/app_event-driven-framework.png "Application Layout")

What to do:
1. Accept the tutorial assignment and clone the repository
1. ``cd $ROBOT_CODE/tutorial_event-driven-framework && mkdir build && cd build && cmake ..``
1. ``make install``
2. Open a ``yarpmanager`` and load the event-driven-framework application
3. Run all
4. Load the vvv18-eventdriven-dataset/1 into the ``yarpdataplayer``
5. Connect all YARP port connections
6. Compare the event-camera, traditional camera and iCub encoder values.
7. Modify the vPreProcessing parameters.
7. Modify the vFramer parameters


