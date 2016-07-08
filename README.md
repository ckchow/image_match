From looking at a few sample images, it looks like the principal image
distortions are blurs, rotations, color transforms (to gray, to other space).
I tried to design a matching pipeline that would be resistant to those effects.

My baseline approach was using various perceptual hashing algorithms that
use grayscale and various downsampling approaches (wavelet, DCT, average)
to compress images down to a 64x64 representation that is invariant under
small shifts, color transforms, rotations, and other distortions. Performance
was around 0.29 when only assigning each original image a perfect match.
Performance could be improved by selecting suboptimal matches instead of only
optimal matches, but I suspected the severe rotations in the modified images
were causing misses.

I tried using ORB features and FLANN keypoint / descriptor matching to
perform a more robust match with rotation invariance. ORB is a feature detector
based on FAST (a keypoint detector) and BRIEF (a rotation-robust keypoint descriptor)
that can quickly generate a set of keypoints and descriptors (vectors of some dimension).
These keypoints and descriptors are calculated per image and then exhaustively compared
pairwise to determine the best correspondence between original and modified images.

I make use of the Lowe ratio test to ensure each keypoint match is good enough and then I
calculate a match quality (0-1) by comparing the number of matching keypoints to the number of
detected keypoints in the original image.

Improvements:
Figure out whether blurring helps ORB detection / matching or not
Try a metric learning approach with CNNs
Perceptual hash with rotated copy comparisons (i.e. match all rotated copies against one another)