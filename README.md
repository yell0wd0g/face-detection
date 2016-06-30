Users interacting with the world out there using WebCams or other Video Capture Devices have become accustomed to features such as seeing the human faces in the scene being identified and highlighted. This is particularly true in the case of mobile devices, where hardware manufacturers have been supporting these features for quite some time now. Web Apps do not have access to these hardware capabilities, which makes necessary the use of demanding libraries.

Use cases
=========

* Live video feeds would like to identify faces in a captured picture/live feed as highly salient areas for e.g. give hints to image or video encoders.
* Social network webapps would like to quickly identify the human faces in a picture/video and offer the user e.g. the possibility of tagging which name corresponds to what face.
* Face detection is the first step before Face Recognition.
* Fun! you can map glasses, funny hats and other overlays

Possible future use cases
=========================

* Hardware vendors provide detectors for other items in widespread use, notably QR codes and text.

Current Workarounds
===================

* Some Web Apps -gasp- run Face Detection in Javascript. A performance comparison of some such libraries can be found in https://github.com/mtschirs/js-objectdetect#performance.

Potential for misuse
====================

* Face Detection is an expensive operation due to the algorithmic complexity. Many requests, or live stream feed with a certain frame rate could slow down the whole system.

Platform specific implementation notes
======================================

## Mac OS X / iOS

CoreImage library includes a CIDetector class that provides not only Face Detection, but also QR, Text and Rectangles.

[CIDetector class, Mac OS X](https://developer.apple.com/library/mac/documentation/CoreImage/Reference/CIDetector_Ref/)
[CIDetector class, iOS](https://developer.apple.com/library/ios/documentation/CoreImage/Reference/CIDetector_Ref/)

## Android

Android provides a stand alone FaceDetector class. It also has a built-in for detecting on the fly while capturing video or taking photos, as part of the Camera2 API.

[Android FaceDetector](https://developer.android.com/reference/android/media/FaceDetector.html)
[Camera2 CaptureRequest](https://developer.android.com/reference/android/hardware/camera2/CaptureRequest.html#STATISTICS_FACE_DETECT_MODE)

Rough sketch of a proposal
==========================

The WebIDL could look like this:

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

And a potential simple example could work like that.

```
 navigator.detectFaces().then(function(bounding_boxes) {
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


Open questions
==============

