# An introduction to the event-driven framework
We introduce the following topics to understand how the event-driven framework works:

## The Neuromorphic iCub
The purple iCub comes equipped with neuromorphic hardware.
- Within each eye is an Asynchronous Time-based Image Sensor (ATIS) event-driven camera.
- The lens is shared with a high resolution frame-based camera (Python) using a beam-splitter to expose both sensors to the same light. Both cameras "see" the same scene.
- The frame-based cameras are connected to the _icub-head_ computer.
- The event-based cameras are connected to the specialised _icub-zynq_ computer. This chip contains both an FPGA and an ARM CPU that are interfaced. The FPGA connects to the ATIS sensors and the CPU (running Linux) is connected on the iCub's network.

## "Event" review
- An event is something that happens at a precise time. The ATIS produces an event at a pixel location [u, v] given the light falling on the pixel changes (higher or lower) [p]. The event is also addressed to the left or right camera [c].
- See [this](https://www.youtube.com/watch?v=kPCZESVfHoQ) video for a good overview of the operation of dynamic vision sensors.
- Event cameras have: low-power, low-latency, high temporal-resolution, no frame-rate limitiations, and a compressed visual signal.
- We want to use them in robotics for fast, long-lasting autonomous robots.

## Event-driven Framework with YARP
Event-driven data is integrated in YARP so we can do distributed event-driven sensing and processing and integrate with standard iCub sensors as well as behaviour and control modules. The *zynqGrabber* is a YARP module that runs on the _icub-zynq_; it exposes the data from the event-driven cameras on a YARP port. Data is sent in packets of several thousand events wrapped in a ``yarp::os::Bottle`` which we have called an ``ev::vBottle``. Processing modules read packets of events asynchronously using callback functions on the input ports - only when data arrives is processing performed. 

Click: for the [documentation](http://robotology.github.io/event-driven/doxygen/doc/html/index.html) for the event-driven libraries.
Click: for the [main project page](https://github.com/robotology/event-driven).

## Tutorial
In this tutorial we are going to:
1. Introduce the dataset we will use in assignemnts.
2. Introduce the typical data flow through an event-driven application.
3. See what options are available for the ``vPreProcess`` module.
4. See what options are available for visualisation with the ``vFramer`` module.

![Application Layout](https://github.com/vvv-school/tutorial_event-driven-framework/blob/master/misc/app_event-driven-framework.png "Application Layout")



