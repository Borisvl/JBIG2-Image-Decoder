JBIG2-Image-Decoder
=============================

This is a fork of [JPedal's JBIG2 library](http://www.jpedal.org/support_JBIG.php), 
which aims to improve the performance of the original code.

Results
-----------------------------

Version 1 (09.10.2011):

In all samples we have tested, the decoding speed has increased at least by 
factor 2.5.
In some examples a speed up by factor 4.5 was observed.

There is still lot of room for improvements especially in the combine method 
in JBIG2Bitmap, which is most time consuming. 