JBIG2-Image-Decoder
=============================
This project fix bug in MMR decoding in Borisvl/JBIG2-Image-Decoder's fork JPedal's JBIG2 library.

Description
-----------------------------
A pdf [file](https://github.com/afila/JBIG2-Image-Decoder/blob/master/XeroxWorkCentre5230A_Huffman.pdf)
(version 1.4) produced by Xerox WorkCentre 5230(A) with JBIG2 compression (MMR) give error, when attept to decode an [image  stream](https://github.com/afila/JBIG2-Image-Decoder/blob/master/XeroxWorkCentre5230A_Huffman.jb2) from this file:

    Exception in thread "AWT-EventQueue-0" java.lang.ArrayIndexOutOfBoundsException: 30447
	    at org.jpedal.jbig2.io.StreamReader.readByte(StreamReader.java:86)
	    at org.jpedal.jbig2.decoders.JBIG2StreamDecoder.handleSegmentReferredToCountAndRententionFlags(JBIG2StreamDecoder.java:503)
	    at org.jpedal.jbig2.decoders.JBIG2StreamDecoder.readSegmentHeader(JBIG2StreamDecoder.java:457)
	    at org.jpedal.jbig2.decoders.JBIG2StreamDecoder.readSegments(JBIG2StreamDecoder.java:211)
	    at org.jpedal.jbig2.decoders.JBIG2StreamDecoder.decodeJBIG2(JBIG2StreamDecoder.java:173)
	    at org.jpedal.jbig2.JBIG2Decoder.decodeJBIG2(JBIG2Decoder.java:148)
	    at org.jpedal.jbig2.JBIG2Decoder.decodeJBIG2(JBIG2Decoder.java:123)
	    at org.jpedal.jbig2.JBIG2Decoder.decodeJBIG2(JBIG2Decoder.java:108)
	    at org.jpedal.jbig2.JBIG2Decoder.decodeJBIG2(JBIG2Decoder.java:98)
	    at org.jpedal.jbig2.examples.viewer.JBIG2Viewer.openFile(JBIG2Viewer.java:274)
	    at org.jpedal.jbig2.examples.viewer.JBIG2Viewer.access$0(JBIG2Viewer.java:252)
	    at org.jpedal.jbig2.examples.viewer.JBIG2Viewer$1.actionPerformed(JBIG2Viewer.java:163)
	    at javax.swing.AbstractButton.fireActionPerformed(Unknown Source)
	    at javax.swing.AbstractButton$Handler.actionPerformed(Unknown Source)
	    at javax.swing.DefaultButtonModel.fireActionPerformed(Unknown Source)
	    at javax.swing.DefaultButtonModel.setPressed(Unknown Source)
	    at javax.swing.plaf.basic.BasicButtonListener.mouseReleased(Unknown Source)
	    at java.awt.AWTEventMulticaster.mouseReleased(Unknown Source)
	    at java.awt.Component.processMouseEvent(Unknown Source)
	    at javax.swing.JComponent.processMouseEvent(Unknown Source)
	    at java.awt.Component.processEvent(Unknown Source)
	    at java.awt.Container.processEvent(Unknown Source)
	    at java.awt.Component.dispatchEventImpl(Unknown Source)
	    at java.awt.Container.dispatchEventImpl(Unknown Source)
	    at java.awt.Component.dispatchEvent(Unknown Source)
	    at java.awt.LightweightDispatcher.retargetMouseEvent(Unknown Source)
	    at java.awt.LightweightDispatcher.processMouseEvent(Unknown Source)
	    at java.awt.LightweightDispatcher.dispatchEvent(Unknown Source)
	    at java.awt.Container.dispatchEventImpl(Unknown Source)
	    at java.awt.Window.dispatchEventImpl(Unknown Source)
	    at java.awt.Component.dispatchEvent(Unknown Source)
	    at java.awt.EventQueue.dispatchEventImpl(Unknown Source)
	    at java.awt.EventQueue.access$200(Unknown Source)
	    at java.awt.EventQueue$3.run(Unknown Source)
	    at java.awt.EventQueue$3.run(Unknown Source)
	    at java.security.AccessController.doPrivileged(Native Method)
	    at java.security.ProtectionDomain$1.doIntersectionPrivilege(Unknown Source)
	    at java.security.ProtectionDomain$1.doIntersectionPrivilege(Unknown Source)
	    at java.awt.EventQueue$4.run(Unknown Source)
	    at java.awt.EventQueue$4.run(Unknown Source)
	    at java.security.AccessController.doPrivileged(Native Method)
	    at java.security.ProtectionDomain$1.doIntersectionPrivilege(Unknown Source)
	    at java.awt.EventQueue.dispatchEvent(Unknown Source)
	    at java.awt.EventDispatchThread.pumpOneEventForFilters(Unknown Source)
	    at java.awt.EventDispatchThread.pumpEventsForFilter(Unknown Source)
	    at java.awt.EventDispatchThread.pumpEventsForHierarchy(Unknown Source)
	    at java.awt.EventDispatchThread.pumpEvents(Unknown Source)
	    at java.awt.EventDispatchThread.pumpEvents(Unknown Source)
	    at java.awt.EventDispatchThread.run(Unknown Source)


Reason
--------------------------------
See 6.2.6 **Decoding using MMR coding** of [JBIG2 specification](http://www.hlevkin.com/Standards/fcd14492.pdf) for detail.

If MMR is 1, the generic region decoding procedure is identical to an MMR (ModifiedModified READ) decoder
described in ITU-T Recommendation T.6, with the following exceptions:
 An invocation of the generic region decoding procedure with MMR equal to 1 `shall consume an integral
number of bytes, beginning and ending on a byte boundary`. This may involve skipping over some bits in
the last byte read.

Fix
--------------------------------
```java
//GenericRegionSegment.java
//bitmap.readBitmap(useMMR, template, typicalPredictionGenericDecodingOn, false, null, genericBAdaptiveTemplateX, genericBAdaptiveTemplateY, useMMR ? 0 : length - 18);
bitmap.readBitmap(useMMR, template, typicalPredictionGenericDecodingOn, false, null, genericBAdaptiveTemplateX, genericBAdaptiveTemplateY, useMMR ? bytesRead : length - 18);
```
