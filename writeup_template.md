# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of total 6 steps. 

* Convert image to gray scale
I made use of the helper function grayscale() that is provided as part of the challenge to convert the actual image to gray scale image

* Applied gaussian blur to the image
I found a kernel size 5 returned best image output for canny to perform well

* Performed canny edge detection
I then passed the gaussian blur image response to canny with a good threshold interval of 80:160 maintained the recommended 2:1 ratio.

* Selected region_of_interest() with polygon points
I wrote a helper method that takes an image and then uses image shape rows and cols to estimate best fit polygon. The calculation is based on the assumption that the car should be centered in the lane (I thought this can later be used to indicate that car is not properly in lane if the car goes to far away from the center). I also assumed that the camera is mounted on the car in the center horizontally.

* Performed hough_tranform() on the canny output image 
I played alot with proper combination of rho, threshold, min_line_len, max_line_gap values to get the best output. I am assuming this could've been improved little more to avoid slight fluctations in the final lane line slope. I used high threshold, min_line_len, max_line_gap values to avoid smaller lines/dots. 

* Called weighted_img() with hough tranform output image
Finally, i used the weighted_img() to add the final transformed image with cv2::lines on top of the actual image.

In order to draw a single line on the left and right lanes, I modified the draw_lines() to filter and extrapolate lines into left and right lane properly. These are the few important steps that I performed.

* Capture slope and intercept values for each line
    * Added to helper methods get_line_slope() and get_line_intercept()
* Capture max, min slope and intercept values (which are basically the extreme ends of the lines) 
* Filter each slope_intercepts nparray values into left and right lanes line arrays
    * I first picked with left lanes lines that has slopes close(0.15) to max slope value and intercept values that are close to max intercept(or the extreme line)  - i guess this can be improved alot
    * I did the same for the right lanes lines by filtering lines that have slopes close(0.15) to min slope value and intercept values close to min intercept
* Calculate avg slope and intercept for left and right lanes
    * Here, I calucated the avg slope and intercept values for right and left lanes seperately. I thought this would give better fit final line
* Finally, I called cv2.line for both left and right lane lines


If you'd like to include images to show how the pipeline works, here is how to include an image: 


### 2. Identify potential shortcomings with your current pipeline


These are the shortcomings i noticed by analyzing pipeline performance against test videos

* I noticed that the lane lines(especially left lane line) are fluctuating a little sometimes.
    *I am guessing this had to deal with the final lane line slope estimation.
* I noticed that the pipeline processor is not accurate when the lane line colors are not clear.
    *I should've properly done more work(keep only interested colors like yellow and white) on preprocessing the image before sending the image for canny detection.
* I noticed that the lanes lines gets disorientated when the car went over a road bump or disturbance.
    *Little unsure what could be done here.


### 3. Suggest possible improvements to your pipeline

* Final lane lines slope estimation should become more robust
* Apply few more colors masks to filter out unneeded colors to reduce noise during canny edge estimation
* Extrapolation should be able to handle slight hiccups(road bumps etc) between frames 

