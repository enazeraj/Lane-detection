# **Finding Lane Lines on the Road** 

## Writeup of Lane-detection by Egi Nazeraj

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/Output_whiteCarLaneSwitch.jpg

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps: 

First, I created 2 figures: one with a yellow mask and another one with a white mask.

The yellow mask was added in order to have the yellow tracking also under shadow conditions. To do that I had first to convert the image in HSV.

The white mask was derived by converting the image first to grey scale

Second, I joined the two masks in a final image by summing up the two matrices

Third: I blureed the image with a gaussian filter with a kernel of 7. A bit higher in order to smooth the image with shadows

Fourth: Applied the Canny function to get the edges with a range on the gradient of 50-150

Fifth: I applied the region of interest, which has a trapezoidal shapes. The larger base is the bottom of the image, while the short base is from 40% to 60% of the image. The height of the trapezoid is 60% of the image.

Sixth: The Hough lines are then detected using the triplet (40, 30, 80). The lenght of 30 was selected to take into account the intermittent line, while the distance of 80 was selected to include cases where the line is discontinuous due to shadows.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function:

This function stores the slope of each line and assign it to a matrix M 2xlen(lines)
The first row expresses the slope on the right-side and second row on the left-side.

Right and left are decided on the base of the value of x1.
In addition, to reject unfeasible lines, all the slopes for which |m| < 0.3 are not stored. This was particularly true for the case where horizontal white lines were crossing the yellow lines

The equivalent slope of the final line is the average of the different slopes in M, while the point at which pass the line is defined as the average x and y in the two quadrant of the image (left and right quadrant). Finally, the second point (x2, y2) at which the line of slope m_avg passes is calculated as:
    
    x2 = (y2-y_avg)/m_avg + x_avg
    
    Where y2 is equal to the bottom of the extreme of the Region of Interest


![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


The draw_lines() function needs to have lines in both right and left side. If it doesnt there will be a division for zero that will make the program to crash. The case where on one side the lines are not present will reflect cases where the darkness (or shadow) is present for long time, for instance in a tunnel.


### 3. Suggest possible improvements to your pipeline

A way that I was trying to implement was to cache the last value of slope. In this case, if the hough lines doesn't find any line can still draw the one cached beforehand. This will allow the code to run for even long time of darkness. Of course, this will be a problem if the tunnel has also a turn.
