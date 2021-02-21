# Write Up for the "Camera Based 2D Feature Tracking" Project

## MP.0 Mid-Term Report

*Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf.*

This is the report file.

## MP.1 Data Buffer Optimization

*Implement a vector for dataBuffer objects whose size does not exceed a limit (e.g. 2 elements). This can be achieved by pushing in new elements on one end and removing elements on the other end.*

I changed the code so that the front element is removed when the size is equal to the limit before adding a new element. In this way the size is always at most equal to the limit.

## MP.2 Keypoint Detection

*Implement detectors HARRIS, FAST, BRISK, ORB, AKAZE, and SIFT and make them selectable by setting a string accordingly.*

I implemented the Harris detector in the `detKeypointsHarris` function in the `matching2D_Student.cpp` file. I used the `cornerHarris` function in OpenCV and then had to look for the local maxima to returns the keypoints. The FAST, BRISK, ORB, AKAZE and SIFT methods are then implemented in the `detKeypointsModern` function. Their implementation is much simpler as they are either implemented as a simple function (the `FAST` method) or as a `FeatureDetector` instance (all the others methods) in OpenCV. The selection is implemented using `if/else` statements.

The following images show the keypoints detected in the last frame using the different methods.

**SHITOMASI**

![](doc-images/SHITOMASI.png)

**HARRIS**

![](doc-images/HARRIS.png)

**FAST**

![](doc-images/FAST.png)

**BRISK**

![](doc-images/BRISK.png)

**ORB**

![](doc-images/ORB.png)

**AKAZE**

![](doc-images/AKAZE.png)

**SIFT**

![](doc-images/SIFT.png)

## MP.3 Keypoint Removal

*Remove all keypoints outside of a pre-defined rectangle and only use the keypoints within the rectangle for further processing.*

I have used the `erase/remove_if` pattern to delete the keypoints that are outside the given rectangle.

## MP.4 Keypoint Descriptors

*Implement descriptors BRIEF, ORB, FREAK, AKAZE and SIFT and make them selectable by setting a string accordingly.*

I used an `if/else` statement to decide between the various `FeatureDetector` instances. I used the default arguments for all the constructors.

## MP.5 Descriptor Matching

*Implement FLANN matching as well as k-nearest neighbor selection. Both methods must be selectable using the respective strings in the main function.*

I implemented the FLANN matching in the `matchDescriptors` function. As it only supports floating point input, I had to convert the binary descriptors to floating point ones. Similarly I had to use different norms in the brute force method.

To implement the k-nearest neighbour method it was enough to use the `knnMatch` method instead of the `match` one. I simply selected the first match as the resulting match.

## MP.6 Descriptor Distance Ratio

*Use the K-Nearest-Neighbor matching to implement the descriptor distance ratio test, which looks at the ratio of best vs. second-best match to decide whether to keep an associated pair of keypoints.*

To implement this task I simply added an `if` statement making sure the ratio between the best vs. second best match distances is lower then a threshold of `0.8` (the threshold mentioned later in the project).

## MP.7 Performance Evaluation 1

*Count the number of keypoints on the preceding vehicle for all 10 images and take note of the distribution of their neighborhood size. Do this for all the detectors you have implemented.*

Not all the keypoints inside the rectangle are on the preceding vehicle. Some of them are indeed on the street or on other vehicles. I think the ratio of keypoints on the car vs all the keypoints is quite similar between the various detectors, but this is based on the brief observation of the images instead of data. All methods select keypoints mostly on the border regions of the car (i.e. the interior, the lights and the actual border) and on the license plate.

The following table show the numbers of keypoints for the different detectors. The table is also contained in the `performance.ods` (and `performance.xls`) files in the `KEYPOINTS NUMBER` sheet.

| Image | SHITOMASI | HARRIS |  FAST | BRISK |   ORB | AKAZE |  SIFT |
| ----: | --------: | -----: | ----: | ----: | ----: | ----: | ----: |
|     0 |       125 |     11 |   149 |   264 |    92 |   166 |   138 |
|     1 |       118 |     13 |   152 |   282 |   102 |   157 |   132 |
|     2 |       123 |     19 |   150 |   282 |   106 |   161 |   124 |
|     3 |       120 |     21 |   155 |   277 |   113 |   155 |   137 |
|     4 |       120 |     20 |   149 |   297 |   109 |   163 |   134 |
|     5 |       113 |     20 |   149 |   279 |   125 |   164 |   140 |
|     6 |       114 |     17 |   156 |   289 |   130 |   173 |   137 |
|     7 |       123 |     28 |   150 |   272 |   129 |   175 |   148 |
|     8 |       111 |     22 |   138 |   266 |   127 |   177 |   159 |
|     9 |       112 |     21 |   143 |   254 |   128 |   179 |   137 |
| AVG.  |     117.9 |   19.2 | 149.1 | 276.2 | 116.1 |   167 | 138.6 |

Ignoring the speed of the various methods, the BRISK detector, followed by the AKAZE and FAST ones, detects the most keypoints.

The Shi-Tomasi, Harris and FAST methods have fixed neighbourhood sizes. The BRISK, ORB, AKAZE and SIFT detectors have instead keypoints with different neighbourhood sizes. Between these last methods, AKAZE has the less variation in size while BRISK and ORB have the bigger. SIFT is somewhat in the middle. ORB seems to have many concentric keypoints with different sizes. BRISK is somewhat similar, but while the circles are often contained in others, they seem to have a bigger variation in the centres. The other detectors do not share this property.

## MP.8 Performance Evaluation 2

*Count the number of matched keypoints for all 10 images using all possible combinations of detectors and descriptors. In the matching step, the BF approach is used with the descriptor distance ratio set to 0.8.*

The BRISK, AKAZE and FAST detectors are the one with more matches for all descriptors because they are the one starting with more keypoints to begin with. The following table show the average matches in all combinations

|       | SHITOMASI | HARRIS |  FAST  |  BRISK |   ORB |  AKAZE |  SIFT |
| :---- | --------: | -----: | -----: | -----: | ----: | -----: | ----: |
| BRISK |     85.22 |  15.33 |  99.89 | 174.44 | 83.44 | 135.00 | 65.78 |
| BRIEF |    104.89 |  17.22 | 122.11 | 189.33 | 60.56 | 140.67 | 78.00 |
| ORB   |    100.78 |  17.44 | 120.11 | 167.78 | 84.56 | 131.78 | ERROR |
| FREAK |     85.11 |  15.44 |  97.89 | 169.56 | 46.78 | 132.00 | 66.22 |
| AKAZE |     ERROR |  ERROR |  ERROR |  ERROR | ERROR | 139.89 | ERROR |
| SIFT  |    103.00 |  17.33 | 116.22 | 182.89 | 84.78 | 141.11 | 88.89 |

For the Shi-Tomasi, FAST and BRISK, the BRIEF descriptor is the one working the best with either ORB or SIFT as close seconds. For the Harris detector the best descriptor is ORB but BRIEF and SIFT are close. For the ORB, AKAZE and SIFT detectors, the SIFT descriptor is the best. For AKAZE and SIFT the BRIEF descriptor is a close second, while for the ORB detector the second best is the ORB descriptor and BRIEF is not particularly good.

Additional tables are located in the `performance.ods` (and `performance.xls`) file.

## MP.9 Performance Evaluation 3

*Log the time it takes for keypoint detection and descriptor extraction. The results must be entered into a spreadsheet and based on this data, the TOP3 detector / descriptor combinations must be recommended as the best choice for our purpose of detecting keypoints on vehicles.*

The following table summarise the performance I got from the different detectors. All times are in ms.

|       | SHITOMASI | HARRIS |  FAST  |  BRISK |   ORB |  AKAZE |  SIFT |
| :---- | --------: | -----: | -----: | -----: | ----: | -----: | ----: |
|  AVG  |     14.07 |  13.23 |   3.34 |  29.53 | 12.10 |  66.07 | 74.06 |
| STDEV |      5.69 |   5.07 |   1.61 |   0.71 |  5.34 |   9.02 | 10.08 |

With the exception of the BRISK detector, I got a lot of variance in the performance measurements of the various methods. FAST is the faster detector with about 4ms for image followed by ORB, SHITOMASI and HARRIS at about 13ms. BRISK follows with about 30ms while AKAZE and SIFT are the slower with approximately 70ms for image.

The following table lists the average time taken by the descriptors for all combinations of detector/descriptors. All times are in ms.

|       | SHITOMASI | HARRIS | FAST  | BRISK |   ORB | AKAZE |  SIFT |
| :---- | --------: | -----: | ----: | ----: | ----: | ----: | ----: |
| BRISK |      1.21 |   0.67 |  1.62 |  2.33 |  1.54 |  1.39 |  1.51 |
| BRIEF |      0.59 |   0.34 |  1.20 |  0.63 |  0.56 |  0.55 |  0.90 |
| ORB   |      3.72 |   3.07 |  4.40 |  7.96 |  9.77 |  6.42 | ERROR |
| FREAK |     25.44 |  21.56 | 33.15 | 21.58 | 26.05 | 21.17 | 24.78 |
| AKAZE |     ERROR |  ERROR | ERROR | ERROR | ERROR | 46.21 | ERROR |
| SIFT  |     10.06 |  13.22 | 21.23 | 17.94 | 29.15 | 12.63 | 52.69 |

The variance in these times is very high in some cases and low in others. I haven't included the standard deviation for space and simplicity. BRIEF, followed by BRISK and ORB, is the fastest descriptor. FREAK, AKAZE and SIFT are much slower with times often higher than 20ms for each image.

The following table show the total times for the combinations:

|       | SHITOMASI | HARRIS | FAST  | BRISK |   ORB |  AKAZE |   SIFT |
| :---- | --------: | -----: | ----: | ----: | ----: | -----: | -----: |
| BRISK |     15.28 |  13.90 |  4.96 | 31.86 | 13.64 |  67.46 |  75.57 |
| BRIEF |     14.66 |  13.57 |  4.54 | 30.16 | 12.66 |  66.62 |  74.96 |
| ORB   |     17.79 |  16.30 |  7.74 | 37.49 | 21.87 |  72.49 |  ERROR |
| FREAK |     39.51 |  34.79 | 36.49 | 51.11 | 38.15 |  87.24 |  98.84 |
| AKAZE |     ERROR |  ERROR | ERROR | ERROR | ERROR | 112.28 |  ERROR |
| SIFT  |     24.13 |  26.45 | 24.57 | 47.47 | 41.25 |  78.70 | 126.75 |

The fastest combinations are FAST/BRIEF, FAST/BRISK and FAST/ORB.

In the following table I divided the number of matches with the average speed to get a possible measure of quality of the combination.

|       | SHITOMASI | HARRIS | FAST  | BRISK |   ORB |  AKAZE |   SIFT |
| :---- | --------: | -----: | ----: | ----: | ----: | -----: | -----: |
| BRISK |      5.58 |   1.10 | 20.14 |  5.48 |  6.12 |   2.00 |   0.87 |
| BRIEF |      7.15 |   1.27 | 26.89 |  6.28 |  4.78 |   2.11 |   1.04 |
| ORB   |      5.66 |   1.07 | 15.52 |  4.47 |  3.87 |   1.81 |  ERROR |
| FREAK |      2.15 |   0.44 |  2.68 |  3.31 |  1.23 |   1.51 |   0.67 |
| AKAZE |     ERROR |  ERROR | ERROR | ERROR | ERROR |   1.25 |  ERROR |
| SIFT  |      4.27 |   0.65 |  4.73 |  3.85 |  2.06 |   1.79 |   0.70 |

According to this last metric the best combination is FAST/BRIEF, followed by FAST/BRISK and FAST/ORB as they are the fastest and FAST was the third detector based on the number of matches. The BRISK/BRIEF combination is the one with the biggest number of matches and while slower then many, its matches/speed ratio is still quite high being the fifth in the table (the fourth is SHITOMASI/BRIEF).

IMHO this is not the best way to measure or compare performances between different methods. First of all, considering the number of keypoints in the rectangle are a small fraction of all the detected keypoints in the image (10-20%), it would have been better to crop the image before doing the detection. In this way the detector times would have been lower and thus their weight in our decision. This process also required a lot of manual work that could have been lowered by properly designing the code for automation. In a real test the performance should have been compared on the actual machine and not on the personal computer as the platforms are probably very different.

The performance goals of the project are also not well defined. The task requires to find the TOP3 detector/descriptor combinations, but it does not specify according to what criteria they should be considered the best. I used the ratio between the number of matches and the elapsed time, but depending on the situation this may not be a good metric for this problem.
