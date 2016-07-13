Photos and images constitute the largest chunk of the Web, and many include recognisable features, such as human faces. Detecting these features is computationally expensive, but would lead to interesting use cases e.g. face tagging or detection of high saliency areas. Also, users interacting with WebCams or other Video Capture Devices have become accustomed to camera-like features such as the ability to focus directly on human faces on the screen of their devices. This is particularly true in the case of mobile devices, where hardware manufacturers have long been supporting these features. Unfortunately, Web Apps do not yet have access to these hardware capabilities, which makes the use of compuationally demanding libraries necessary.

Use cases
=========

* Live video feeds would like to identify faces in a picture/video as highly salient areas to e.g. give hints to image or video encoders.
* Social network pages would like to quickly identify the human faces in a picture/video and offer the user e.g. the possibility of tagging which name corresponds to which face.
* Face detection is the first step before Face Recognition: detected faces are used for the recognition phase, greatly speeding the process. 
* Fun! you can map glasses, funny hats and other overlays on top of the detected faces

Possible future use cases
=========================

* Hardware vendors provide detectors for other items in widespread use, notably QR codes and text.

Current Workarounds
===================

* Some Web Apps -gasp- run Face Detection in Javascript. A performance comparison of some such libraries can be found in https://github.com/mtschirs/js-objectdetect#performance.

Potential for misuse
====================

* Face Detection is an expensive operation due to the algorithmic complexity. Many requests, or demanding systems like a live stream feed with a certain frame rate, could slow down the whole system or greatly increase power consumption.

Platform specific implementation notes
======================================

## Mac OS X / iOS

CoreImage library includes a `CIDetector` class that provides not only Face Detection, but also QR, Text and Rectangles.

* [CIDetector class, Mac OS X](https://developer.apple.com/library/mac/documentation/CoreImage/Reference/CIDetector_Ref/)
* [CIDetector class, iOS](https://developer.apple.com/library/ios/documentation/CoreImage/Reference/CIDetector_Ref/)

## Android

Android provides a stand alone `FaceDetector` class. It also has a built-in for detecting on the fly while capturing video or taking photos, as part of the `Camera2`s API.

* [Android FaceDetector](https://developer.android.com/reference/android/media/FaceDetector.html)
* [Camera2 CaptureRequest](https://developer.android.com/reference/android/hardware/camera2/CaptureRequest.html#STATISTICS_FACE_DETECT_MODE)

Rough sketch of a proposal
==========================

```
typedef (HTMLImageElement or
         HTMLVideoElement or
         HTMLCanvasElement or
         Blob or
         ImageData or
         ImageBitmap) ImageBitmapSource;

partial interface navigator {
  Promise <sequence<DOMRect>> detectFaces(ImageBitmapSource);
};
```

Usage
=====

Simple example

```
 navigator.detectFaces(image).then(function(bounding_boxes) {
  for (DOMRect face :  bounding_boxes) {
    console.log("Face detected at (" + face.x + "x" + face.y + ") with size (" +
        face.width + "x" + face.height + ")";
  }
 }).catch(function() {
   alert("Face detection not supported in your platform");
 });
```

Notes
=====

* Using a particular Face Detector does not preclude using others, in this case the hardware provided can provide seeds or weights for user-defined ones.
* Why does Face Detection having such terrible Complexity? The most/best typical algorithm used is the so-called Viola-Jones that uses a cascade of classifiers of different sizes and gives a horrendous O(n^4) - this [video](https://vimeo.com/12774628) exemplifies how the detection process works.

Open questions
==============

