**Finding Lane Lines on the Road**

The goals / steps of this project:
* Make a pipeline that finds lane lines on the road
* Write a report


---

### Reflection

Using image processing, video, and computer vision tools, the problem was factored into a pipeline of filter and transform steps. The first step converts the image (or single frame of video) from color to grayscale, then smooths it with a Gaussian filter. Canny edge detection is applied and cropped to a subset region-of-interest (ROI). These edges are grouped into lines using a Hough transform. The lines are then segregated into left and right using the sign of the slope. Lines are restricted to an interval of slope representing the minimum viable angle from horizontal. Slopes (m) and intercepts (b) in the familiar equation of a line $y=mx+b$ are averaged from the left and right groups and extrapolated from the top to bottom of the ROI. Finally, the lane lines are drawn on a blank image and this is overlain on the original image using a weighted sum.

Later the pipeline was modified to attempt to eliminate errors by comparing the slopes and intercepts through a window of prior frames. This was implemented via a rolling average.

The example frame below is overlain with the outline of the ROI, the raw output of the Hough lines, and the left and right lane lines.

[image1]: ./examples/testframe2.png "Analysis output"

![Example frame][image1]


### 2. Shortcomings with the current pipeline

The original pipeline, before inter-frame comparison was added, had errors in some frames where the calculated lane line was off by a large value. The inter-frame averaging was added to attempt to eliminate these outliers. Actually, this just spreads the error over the future frames until the error drops out of the window.

Another effect of the inter-frame averaging is to add hysteresis to videos that can be seen when the car goes over bumps. The calculated lane slowly bounces up and down, eventually settling back into the correct position.

### 3. Possible improvements to the pipeline

It seemed like it could save time to crop the image as the first step. However, waiting until after Canny edge detection, avoids artifacts that could be created at the margins of the ROI.

The algorithm still occasionally calculates wrong results in a few frames. Better outlier detection both within the frame and inter-frame, could detect and fix these. Eliminating the inter-frame averaging or replacing it with better outlier detection would remove or reduce the hysteresis.

The greatest improvement could be to automate the setting of the controlling parameters. Perhaps even allowing them to be automatically adjusted during processing. The current parameters are set by a combination of measurements and trial-and-error and are tailored to each test image or first frame of the video. As an example, the slope could be estimated from the bottom-left corner to the center of the top of the ROI (theoretically lines converging at the horizon). Thus, it is determined by the ROI and could be specific to the camera, as opposed to any test imagery.

This was tested in the challenge video with good results.

[video1]: ./examples/challeng0.jpg "Challenge video with lanes"
[![Example video][video1]](http://localhost:8888/notebooks/examples/challenge.mp4)
