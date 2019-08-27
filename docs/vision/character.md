# Character Recognition
Authors: Akanksha
Date: 8/01/2019

1) Introduction related to the domain area of the project

Our project is an extended work on the blooming Computer Vision which was related to the implementation of a version of autonomous robot using Roomba with Raspberry PIs.  We implement a system capable of accounting for different characters present in an image, specially room numbers. We have used the MNIST dataset for this purpose.

2) Literature Survey/background search/study of similar projects

We have gone through similar projects in each module (i.e text detection, word segmentation and character prediction).After going through various links, implementing one, getting stuck in between and figuring out other ways as to how to get rid of the errors, we finally went with East text detector for the detection of text from an image which was further followed by segmentation and prediction.

3) Project

Code can be found here: https://github.com/parallel-ml/wordSegmentation

Diagram:

Input Image -> Text Detection -> Word Segmentation -> Characters with Bounding Boxes -> Predicted Output

4) References

https://github.com/EN10/KerasMNIST
https://www.pyimagesearch.com/2018/08/20/opencv-text-detection-east-text-detector/
