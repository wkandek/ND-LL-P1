# **Finding Lane Lines on the Road** 


**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection


My pipeline consisted of 5 steps:

1. image to grayscale
2. gaussian blur
3. canny for gradient lien detection
4. limit image area of interest
5. hough transform to detect the lines in the image

The parameters for the pipeline came from the 1st part of the project where single images were processed and were then
tuned for the videos:

- blurkernel = 5
- cannylow = 50
- cannyhigh = 150
- maskpoly = np.array([[(100,imgshape[0]),(460,330),(540,330),(imgshape[1],imgshape[0])]], dtype=np.int32)
- houghrho = 1 
- houghtheta = 3.14/180
- houghthreshold = 50 
- houghminlen = 150
- houghmaxgap = 80

This leaves one with a set of lines, but there are too many lines in the image that do not apply to the problem.

In order to draw a single line on the left and right lanes, I looked at selectting the right lines from the the set of lines found. I applied:

- absolute slope between 0.5 and 0.9 = eliminates horizontal lines for example
- partition between lines on the left (slope negative) and right (slope positive)
- extrapolate the lines to the lower image border, and an arbitrary line, roughly in the middle of the image as
an upper border by using y = mx+b, i.e. we know the y values and can calcluate the x values
- average the x values

I copied the draw_lines() function to draw_lines_epo() and modifed the code to apply these criteria to draw the line

This works ok, but sometimes there are no lines found, often when the line is dashed. I implemented a "memory" that
keeps track of the last line drawn and uses that one if no new line could be found (global variables dlx1, dlx2, drx1, drx2)

This produced good results for the 2 test videos solidWhite and solidYellow.

For the challenge video, it caused a problem when the contrast changes on the lighter surface (a bridge maybe). I experimented with adding a steering angle to the masking function,  with the idea to follow the curving road a bit more. The fucntion would shift the masking polyline to the right or left depending on a supplied angle, but that did not provide much improvement.

Changing the Gaussian blur to 11 (after some research on the Internet) solved the problem.


### 2. Identify potential shortcomings with your current pipeline

I can see a number of problems with the pipeline:

1. Parameters were found through experimentation and adapted to work with the 3 videos. Different situations (lighting, nighttime, an overpass) will most likely not work. 

2. A sharper turn in the road will be outside of the masking area and would not be detected.

3. Different lane markers could generate problems. For example botts dots or other markers that might be in use in different countries. 

### 3. Suggest possible improvements to your pipeline

I think the steering angle inclusion into the shape of masking polygon is a valid idea to adapt to curving roads.

Getting more video footage from different roads around the world could help in making the detection more robust

### Files:
- solidWhiteRight.mp4 - annotated file
- solidYellowLeft.mp4 - annotated file

### Comments
Interactions on slack have been very helpful to get ideas on how to solve the issues that poppped up. Very promising.
