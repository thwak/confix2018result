ConFix generated patches and their information.  
For each bug, differences between buggy code and human-written/ConFix-generated are given.  

For each ConFix patch, the following information is also presented.  

Assessment of patches including the reason.  
**Identical** - ConFix patch is identical to Human patch in code level.  
**Equiv.** - ConFix patch is semantically equivalent to Human patch.  
**Acceptable** - ConFix patch addresses the same issue as Human patch.  
**Plausible** - ConFix patch doesn't properly address the issues same as Human patch.  

For Plausible patches, we also checked whether a patch is **Informative**.   
**Partial** - ConFix patch partial fixes the issues addressed by Human patch.  
**Regression** - ConFix path addresses the same issues as Human patch, but introduces some regression.  
**SameLoc** - ConFix patch modifies the same part of code fragment as Human patch does.  

```
Seed - a seed value used to generated the patch  
PTLRH/PLRT - pool names (context types) used to generate the patch  
PatchNum:n - the patch is n-th candidate  
Time - execution time to generate the patch  
CompileError - the number of candidates with compile errors until the patch was generated  
TestFailure - the number of candidates with test failures until the patch was generated  
```

# Patches and Information

# Chart

## chart1

### Human

```
---	chart/chart1/buggy/org/jfree/chart/renderer/category/AbstractCategoryItemRenderer.java
+++	chart/chart1/human/org/jfree/chart/renderer/category/AbstractCategoryItemRenderer.java
@@ -1794,7 +1794,7 @@
         }
         int index = this.plot.getIndexOf(this);
         CategoryDataset dataset = this.plot.getDataset(index);
-        if (dataset != null) {
+        if (dataset == null) {
             return result;
         }
         int seriesCount = dataset.getRowCount();
```
### ConFix

**Identical**  
ConFix patch modifies the same if condition `dataset != null` to `dataset == null`.

```
Seed:53
PTLRH
PatchNum:49
Time:1 min. 40 sec.
CompileError:43
TestFailure:5
```
```
---	chart/chart1/buggy/org/jfree/chart/renderer/category/AbstractCategoryItemRenderer.java
+++	chart/chart1/confix/org/jfree/chart/renderer/category/AbstractCategoryItemRenderer.java
@@ -1794,7 +1794,7 @@
         }
         int index = this.plot.getIndexOf(this);
         CategoryDataset dataset = this.plot.getDataset(index);
-        if (dataset != null) {
+        if (dataset == null) {
             return result;
         }
         int seriesCount = dataset.getRowCount();
```
## chart11

### Human

```
---	chart/chart11/buggy/org/jfree/chart/util/ShapeUtilities.java
+++	chart/chart11/human/org/jfree/chart/util/ShapeUtilities.java
@@ -272,7 +272,7 @@
             return false;
         }
         PathIterator iterator1 = p1.getPathIterator(null);
-        PathIterator iterator2 = p1.getPathIterator(null);
+        PathIterator iterator2 = p2.getPathIterator(null);
         double[] d1 = new double[6];
         double[] d2 = new double[6];
         boolean done = iterator1.isDone() && iterator2.isDone();
```
### ConFix

**Equiv.**  
Human patch updates a variable `p1` to `p2`, so that `iterator2` contains the path iterator of `p2`.  
ConFix patch also updates `p1` to `p2`, but for `iterator1`.  
Since remaining code simply compares `iterator1` and `iterator2`, swapping these two produces the same results.  

```
Seed:1
PTLRH
PatchNum:17
Time:1 min. 38 sec.
CompileError:10
TestFailure:6
```
```
---	chart/chart11/buggy/org/jfree/chart/util/ShapeUtilities.java
+++	chart/chart11/confix/org/jfree/chart/util/ShapeUtilities.java
@@ -72,6 +72,7 @@
 import java.awt.geom.Point2D;
 import java.awt.geom.Rectangle2D;
 import java.util.Arrays;
+import java.util.Set;
 
 /**
  * Utility methods for {@link Shape} objects.
@@ -271,7 +272,7 @@
         if (p1.getWindingRule() != p2.getWindingRule()) {
             return false;
         }
-        PathIterator iterator1 = p1.getPathIterator(null);
+        PathIterator iterator1 = p2.getPathIterator(null);
         PathIterator iterator2 = p1.getPathIterator(null);
         double[] d1 = new double[6];
         double[] d2 = new double[6];
```
## chart13

### Human

```
---	chart/chart13/buggy/org/jfree/chart/block/BorderArrangement.java
+++	chart/chart13/human/org/jfree/chart/block/BorderArrangement.java
@@ -452,7 +452,7 @@
         h[3] = h[2];
         if (this.rightBlock != null) {
             RectangleConstraint c4 = new RectangleConstraint(0.0,
-                    new Range(0.0, constraint.getWidth() - w[2]),
+                    new Range(0.0, Math.max(constraint.getWidth() - w[2], 0.0)),
                     LengthConstraintType.RANGE, h[2], null,
                     LengthConstraintType.FIXED);
             Size2D size = this.rightBlock.arrange(g2, c4);
```
### ConFix

**Plausible**

```
Seed:58
PTLRH
PatchNum:4756
Time:4 min. 44 sec.
CompileError:4420
TestFailure:335
```
```
---	chart/chart13/buggy/org/jfree/chart/block/BorderArrangement.java
+++	chart/chart13/confix/org/jfree/chart/block/BorderArrangement.java
@@ -447,7 +447,7 @@
                     LengthConstraintType.RANGE, h[2], null,
                     LengthConstraintType.FIXED);
             Size2D size = this.leftBlock.arrange(g2, c3);
-            w[2] = size.width;
+            h[2] = size.width;
         }
         h[3] = h[2];
         if (this.rightBlock != null) {
```
## chart24

### Human

```
---	chart/chart24/buggy/org/jfree/chart/renderer/GrayPaintScale.java
+++	chart/chart24/human/org/jfree/chart/renderer/GrayPaintScale.java
@@ -123,7 +123,7 @@
     public Paint getPaint(double value) {
         double v = Math.max(value, this.lowerBound);
         v = Math.min(v, this.upperBound);
-        int g = (int) ((value - this.lowerBound) / (this.upperBound 
+        int g = (int) ((v - this.lowerBound) / (this.upperBound 
                 - this.lowerBound) * 255.0);
         return new Color(g, g, g);
     }
```
### ConFix

**Equiv.**  
ConFix patch updates `v` to `value`, which provides the same value to `g` like Human patch.  

```
Seed:38
PLRT
PatchNum:322
Time:1 min. 40 sec.
CompileError:216
TestFailure:104
```
```
---	chart/chart24/buggy/org/jfree/chart/renderer/GrayPaintScale.java
+++	chart/chart24/confix/org/jfree/chart/renderer/GrayPaintScale.java
@@ -122,7 +122,7 @@
      */
     public Paint getPaint(double value) {
         double v = Math.max(value, this.lowerBound);
-        v = Math.min(v, this.upperBound);
+        value = Math.min(v, this.upperBound);
         int g = (int) ((value - this.lowerBound) / (this.upperBound 
                 - this.lowerBound) * 255.0);
         return new Color(g, g, g);
```
## chart25

### Human

```
---	chart/chart25/buggy/org/jfree/chart/renderer/category/StatisticalBarRenderer.java
+++	chart/chart25/human/org/jfree/chart/renderer/category/StatisticalBarRenderer.java
@@ -256,6 +256,9 @@
 
         // BAR X
         Number meanValue = dataset.getMeanValue(row, column);
+        if (meanValue == null) {
+            return;
+        }
 
         double value = meanValue.doubleValue();
         double base = 0.0;
@@ -312,7 +315,9 @@
         }
 
         // standard deviation lines
-            double valueDelta = dataset.getStdDevValue(row, column).doubleValue();
+        Number n = dataset.getStdDevValue(row, column);
+        if (n != null) {
+            double valueDelta = n.doubleValue();
             double highVal = rangeAxis.valueToJava2D(meanValue.doubleValue() 
                     + valueDelta, dataArea, yAxisLocation);
             double lowVal = rangeAxis.valueToJava2D(meanValue.doubleValue() 
@@ -341,6 +346,7 @@
             line = new Line2D.Double(lowVal, rectY + rectHeight * 0.25, 
                                      lowVal, rectY + rectHeight * 0.75);
             g2.draw(line);
+        }
         
         CategoryItemLabelGenerator generator = getItemLabelGenerator(row, 
                 column);
@@ -400,6 +406,9 @@
 
         // BAR Y
         Number meanValue = dataset.getMeanValue(row, column);
+        if (meanValue == null) {
+            return;
+        }
 
         double value = meanValue.doubleValue();
         double base = 0.0;
@@ -456,7 +465,9 @@
         }
 
         // standard deviation lines
-            double valueDelta = dataset.getStdDevValue(row, column).doubleValue();
+        Number n = dataset.getStdDevValue(row, column);
+        if (n != null) {
+            double valueDelta = n.doubleValue();
             double highVal = rangeAxis.valueToJava2D(meanValue.doubleValue() 
                     + valueDelta, dataArea, yAxisLocation);
             double lowVal = rangeAxis.valueToJava2D(meanValue.doubleValue() 
@@ -484,6 +495,7 @@
             line = new Line2D.Double(rectX + rectWidth / 2.0d - 5.0d, lowVal,
                                      rectX + rectWidth / 2.0d + 5.0d, lowVal);
             g2.draw(line);
+        }
         
         CategoryItemLabelGenerator generator = getItemLabelGenerator(row, 
                 column);
```
### ConFix

**Plausible**

```
Seed:23
PTLRH
PatchNum:8916
Time:8 min. 20 sec.
CompileError:8480
TestFailure:435
```
```
---	chart/chart25/buggy/org/jfree/chart/renderer/category/StatisticalBarRenderer.java
+++	chart/chart25/confix/org/jfree/chart/renderer/category/StatisticalBarRenderer.java
@@ -203,7 +203,7 @@
         }
         StatisticalCategoryDataset statData = (StatisticalCategoryDataset) data;
 
-        PlotOrientation orientation = plot.getOrientation();
+        PlotOrientation orientation = null;
         if (orientation == PlotOrientation.HORIZONTAL) {
             drawHorizontalItem(g2, state, dataArea, plot, domainAxis, 
                     rangeAxis, statData, row, column);
```
## chart26

### Human

```
---	chart/chart26/buggy/org/jfree/chart/axis/Axis.java
+++	chart/chart26/human/org/jfree/chart/axis/Axis.java
@@ -1189,11 +1189,13 @@
         }
         if (plotState != null && hotspot != null) {
             ChartRenderingInfo owner = plotState.getOwner();
+            if (owner != null) {
                 EntityCollection entities = owner.getEntityCollection();
                 if (entities != null) {
                     entities.add(new AxisLabelEntity(this, hotspot, 
                             this.labelToolTip, this.labelURL));
                 }
+            }
         }
         return state;
 
```
### ConFix

**Plausible**

```
Seed:63
PTLRH
PatchNum:6502
Time:1 hrs. 17 min. 6 sec.
CompileError:5893
TestFailure:608
```
```
---	chart/chart26/buggy/org/jfree/chart/axis/Axis.java
+++	chart/chart26/confix/org/jfree/chart/axis/Axis.java
@@ -896,7 +896,8 @@
      * @see #getPlot()
      */
     public void setPlot(Plot plot) {
-        this.plot = plot;
+        this.label = plot.getNoDataMessage();
+		this.plot = plot;
         configure();
     }
 
```
## chart3

### Human

```
---	chart/chart3/buggy/org/jfree/data/time/TimeSeries.java
+++	chart/chart3/human/org/jfree/data/time/TimeSeries.java
@@ -1054,6 +1054,8 @@
             throw new IllegalArgumentException("Requires start <= end.");
         }
         TimeSeries copy = (TimeSeries) super.clone();
+        copy.minY = Double.NaN;
+        copy.maxY = Double.NaN;
         copy.data = new java.util.ArrayList();
         if (this.data.size() > 0) {
             for (int index = start; index <= end; index++) {
```
### ConFix

**Plausible**

```
Seed:30
PTLRH
PatchNum:11
Time:1 min. 16 sec.
CompileError:8
TestFailure:2
```
```
---	chart/chart3/buggy/org/jfree/data/time/TimeSeries.java
+++	chart/chart3/confix/org/jfree/data/time/TimeSeries.java
@@ -635,7 +635,7 @@
                                      // don't notify anyone, because that
                                      // happens next anyway...
             if (notify) {
-                fireSeriesChanged();
+                findBoundsByIteration();;
             }
         }
 
```
## chart4

### Human

```
---	chart/chart4/buggy/org/jfree/chart/plot/XYPlot.java
+++	chart/chart4/human/org/jfree/chart/plot/XYPlot.java
@@ -4490,6 +4490,7 @@
                     }
                 }
                 
+                if (r != null) {
                     Collection c = r.getAnnotations();
                     Iterator i = c.iterator();
                     while (i.hasNext()) {
@@ -4498,6 +4499,7 @@
                             includedAnnotations.add(a);
                         }
                     }
+                }
             }
         }
 
```
### ConFix

**Plausible**

```
Seed:18
PLRT
PatchNum:12142
Time:1 hrs. 23 min. 41 sec.
CompileError:10648
TestFailure:1492
```
```
---	chart/chart4/buggy/org/jfree/chart/plot/XYPlot.java
+++	chart/chart4/confix/org/jfree/chart/plot/XYPlot.java
@@ -4490,7 +4490,7 @@
                     }
                 }
                 
-                    Collection c = r.getAnnotations();
+                    Collection c = getAnnotations();
                     Iterator i = c.iterator();
                     while (i.hasNext()) {
                         XYAnnotation a = (XYAnnotation) i.next();
```
## chart5

### Human

```
---	chart/chart5/buggy/org/jfree/data/xy/XYSeries.java
+++	chart/chart5/human/org/jfree/data/xy/XYSeries.java
@@ -541,11 +541,15 @@
         if (x == null) {
             throw new IllegalArgumentException("Null 'x' argument.");
         }
+        if (this.allowDuplicateXValues) {
+            add(x, y);
+            return null;
+        }
 
         // if we get to here, we know that duplicate X values are not permitted
         XYDataItem overwritten = null;
         int index = indexOf(x);
-        if (index >= 0 && !this.allowDuplicateXValues) {
+        if (index >= 0) {
             XYDataItem existing = (XYDataItem) this.data.get(index);
             try {
                 overwritten = (XYDataItem) existing.clone();
```
### ConFix

**Plausible**

```
Seed:80
PTLRH
PatchNum:167
Time:1 min. 26 sec.
CompileError:137
TestFailure:29
```
```
---	chart/chart5/buggy/org/jfree/data/xy/XYSeries.java
+++	chart/chart5/confix/org/jfree/data/xy/XYSeries.java
@@ -524,7 +524,7 @@
      * @since 1.0.10
      */
     public XYDataItem addOrUpdate(double x, double y) {
-        return addOrUpdate(new Double(x), new Double(y));
+        return addOrUpdate(new Double(y), new Double(y));
     }
 
     /**
```
## chart6

### Human

```
---	chart/chart6/buggy/org/jfree/chart/util/ShapeList.java
+++	chart/chart6/human/org/jfree/chart/util/ShapeList.java
@@ -108,7 +108,14 @@
         if (!(obj instanceof ShapeList)) {
             return false;
         }
-        return super.equals(obj);
+        ShapeList that = (ShapeList) obj;
+        int listSize = size();
+        for (int i = 0; i < listSize; i++) {
+           if (!ShapeUtilities.equal((Shape) get(i), (Shape) that.get(i))) {
+               return false;
+           }
+        }
+        return true;
 
     }
 
```
### ConFix

**Plausible**

```
Seed:74
PLRT
PatchNum:3068
Time:5 min. 44 sec.
CompileError:2374
TestFailure:692
```
```
---	chart/chart6/buggy/org/jfree/chart/util/ShapeList.java
+++	chart/chart6/confix/org/jfree/chart/util/ShapeList.java
@@ -78,7 +78,8 @@
      * @param shape  the {@link Shape}.
      */
     public void setShape(int index, Shape shape) {
-        set(index, shape);
+        index = 0;
+		set(index, shape);
     }
 
     /**
```
## chart7

### Human

```
---	chart/chart7/buggy/org/jfree/data/time/TimePeriodValues.java
+++	chart/chart7/human/org/jfree/data/time/TimePeriodValues.java
@@ -297,9 +297,9 @@
         }
         
         if (this.maxMiddleIndex >= 0) {
-            long s = getDataItem(this.minMiddleIndex).getPeriod().getStart()
+            long s = getDataItem(this.maxMiddleIndex).getPeriod().getStart()
                 .getTime();
-            long e = getDataItem(this.minMiddleIndex).getPeriod().getEnd()
+            long e = getDataItem(this.maxMiddleIndex).getPeriod().getEnd()
                 .getTime();
             long maxMiddle = s + (e - s) / 2;
             if (middle > maxMiddle) {
```
### ConFix

**Plausible**  
**Informative - Partial**  
ConFix patch delivers one of the two modifications of Human patch.  

```
Seed:83
PTLRH
PatchNum:138
Time:1 min. 15 sec.
CompileError:74
TestFailure:63
```
```
---	chart/chart7/buggy/org/jfree/data/time/TimePeriodValues.java
+++	chart/chart7/confix/org/jfree/data/time/TimePeriodValues.java
@@ -297,7 +297,7 @@
         }
         
         if (this.maxMiddleIndex >= 0) {
-            long s = getDataItem(this.minMiddleIndex).getPeriod().getStart()
+            long s = getDataItem(this.maxMiddleIndex).getPeriod().getStart()
                 .getTime();
             long e = getDataItem(this.minMiddleIndex).getPeriod().getEnd()
                 .getTime();
```
## chart8

### Human

```
---	chart/chart8/buggy/org/jfree/data/time/Week.java
+++	chart/chart8/human/org/jfree/data/time/Week.java
@@ -172,7 +172,7 @@
      */
     public Week(Date time, TimeZone zone) {
         // defer argument checking...
-        this(time, RegularTimePeriod.DEFAULT_TIME_ZONE, Locale.getDefault());
+        this(time, zone, Locale.getDefault());
     }
 
     /**
```
### ConFix

**Plausible**

```
Seed:91
PTLRH
PatchNum:21
Time:1 min. 9 sec.
CompileError:19
TestFailure:1
```
```
---	chart/chart8/buggy/org/jfree/data/time/Week.java
+++	chart/chart8/confix/org/jfree/data/time/Week.java
@@ -195,7 +195,7 @@
         if (locale == null) {
             throw new IllegalArgumentException("Null 'locale' argument.");
         }
-        Calendar calendar = Calendar.getInstance(zone, locale);
+        Calendar calendar = Calendar.getInstance(locale);
         calendar.setTime(time);
 
         // sometimes the last few days of the year are considered to fall in
```
## chart9

### Human

```
---	chart/chart9/buggy/org/jfree/data/time/TimeSeries.java
+++	chart/chart9/human/org/jfree/data/time/TimeSeries.java
@@ -941,7 +941,7 @@
             endIndex = -(endIndex + 1); // this is first item AFTER end period
             endIndex = endIndex - 1;    // so this is last item BEFORE end
         }
-        if (endIndex < 0) {
+        if ((endIndex < 0)  || (endIndex < startIndex)) {
             emptyRange = true;
         }
         if (emptyRange) {
```
### ConFix

**Plausible**  
**Informative - Regression**  
ConFix patch solves the incorrect behaviour when `endIndex < startIndex`, but it does not work same as Human patch when `endIndex == -1`.  

```
Seed:74
PTLRH
PatchNum:4509
Time:4 min. 17 sec.
CompileError:4180
TestFailure:328
```
```
---	chart/chart9/buggy/org/jfree/data/time/TimeSeries.java
+++	chart/chart9/confix/org/jfree/data/time/TimeSeries.java
@@ -941,7 +941,7 @@
             endIndex = -(endIndex + 1); // this is first item AFTER end period
             endIndex = endIndex - 1;    // so this is last item BEFORE end
         }
-        if (endIndex < 0) {
+        if (endIndex < startIndex) {
             emptyRange = true;
         }
         if (emptyRange) {
```
# Closure

## closure10

### Human

```
---	closure/closure10/buggy/com/google/javascript/jscomp/NodeUtil.java
+++	closure/closure10/human/com/google/javascript/jscomp/NodeUtil.java
@@ -1414,7 +1414,7 @@
 
   static boolean mayBeString(Node n, boolean recurse) {
     if (recurse) {
-      return allResultsMatch(n, MAY_BE_STRING_PREDICATE);
+      return anyResultsMatch(n, MAY_BE_STRING_PREDICATE);
     } else {
       return mayBeStringHelper(n);
     }
```
### ConFix

**Plausible**

```
Seed:52
PTLRH
PatchNum:5659
Time:39 min. 27 sec.
CompileError:5129
TestFailure:529
```
```
---	closure/closure10/buggy/com/google/javascript/jscomp/NodeUtil.java
+++	closure/closure10/confix/com/google/javascript/jscomp/NodeUtil.java
@@ -1409,7 +1409,7 @@
    * @returns Whether the results is possibly a string.
    */
   static boolean mayBeString(Node n) {
-    return mayBeString(n, true);
+    return mayBeString(n, false);
   }
 
   static boolean mayBeString(Node n, boolean recurse) {
```
## closure115

### Human

```
---	closure/closure115/buggy/com/google/javascript/jscomp/FunctionInjector.java
+++	closure/closure115/human/com/google/javascript/jscomp/FunctionInjector.java
@@ -694,14 +694,6 @@
 
     Node block = fnNode.getLastChild();
 
-    boolean hasSideEffects = false;
-    if (block.hasChildren()) {
-      Preconditions.checkState(block.hasOneChild());
-      Node stmt = block.getFirstChild();
-      if (stmt.isReturn()) {
-        hasSideEffects = NodeUtil.mayHaveSideEffects(stmt.getFirstChild(), compiler);
-      }
-    }
     // CALL NODE: [ NAME, ARG1, ARG2, ... ]
     Node cArg = callNode.getFirstChild().getNext();
 
@@ -727,9 +719,6 @@
       // For each named parameter check if a mutable argument use more than one.
       if (fnParam != null) {
         if (cArg != null) {
-          if (hasSideEffects && NodeUtil.canBeSideEffected(cArg)) {
-            return CanInlineResult.NO;
-          }
           // Check for arguments that are evaluated more than once.
           // Note: Unlike block inlining, there it is not possible that a
           // parameter reference will be in a loop.
```
### ConFix

**Acceptable**  
The key changes of Human patch are deleting two if statements.  
ConFix patch disables the first if statement by changing its condition, since `fnNode.isReturn()` cannot be `true`.  
The second if statement is disabled by this first change, since one of its condition `hasSideEffects` is declared as `false` and this value is not changed due to the first disabled if statement.  

```
Seed:18
PTLRH
PatchNum:4846
Time:15 min. 22 sec.
CompileError:4577
TestFailure:268
```
```
---	closure/closure115/buggy/com/google/javascript/jscomp/FunctionInjector.java
+++	closure/closure115/confix/com/google/javascript/jscomp/FunctionInjector.java
@@ -698,7 +698,7 @@
     if (block.hasChildren()) {
       Preconditions.checkState(block.hasOneChild());
       Node stmt = block.getFirstChild();
-      if (stmt.isReturn()) {
+      if (fnNode.isReturn()) {
         hasSideEffects = NodeUtil.mayHaveSideEffects(stmt.getFirstChild(), compiler);
       }
     }
```
## closure126

### Human

```
---	closure/closure126/buggy/com/google/javascript/jscomp/MinimizeExitPoints.java
+++	closure/closure126/human/com/google/javascript/jscomp/MinimizeExitPoints.java
@@ -138,10 +138,6 @@
        * can cause problems if it changes the completion type of the finally
        * block. See ECMA 262 Sections 8.9 & 12.14
        */
-      if (NodeUtil.hasFinally(n)) {
-        Node finallyBlock = n.getLastChild();
-        tryMinimizeExits(finallyBlock, exitType, labelName);
-      }
     }
 
     // Just a 'label'.
```
### ConFix

**Plausible**

```
Seed:94
PTLRH
PatchNum:172
Time:2 min. 50 sec.
CompileError:171
TestFailure:0
```
```
---	closure/closure126/buggy/com/google/javascript/jscomp/MinimizeExitPoints.java
+++	closure/closure126/confix/com/google/javascript/jscomp/MinimizeExitPoints.java
@@ -140,7 +140,7 @@
        */
       if (NodeUtil.hasFinally(n)) {
         Node finallyBlock = n.getLastChild();
-        tryMinimizeExits(finallyBlock, exitType, labelName);
+        tryMinimizeExits(finallyBlock, Token.WITH, labelName);
       }
     }
 
```
## closure13

### Human

```
---	closure/closure13/buggy/com/google/javascript/jscomp/PeepholeOptimizationsPass.java
+++	closure/closure13/human/com/google/javascript/jscomp/PeepholeOptimizationsPass.java
@@ -123,8 +123,8 @@
     do {
       Node c = node.getFirstChild();
       while(c != null) {
-        traverse(c);
         Node next = c.getNext();
+        traverse(c);
         c = next;
       }
 
```
### ConFix

**Plausible**

```
Seed:64
PTLRH
PatchNum:1215
Time:5 min. 38 sec.
CompileError:1156
TestFailure:58
```
```
---	closure/closure13/buggy/com/google/javascript/jscomp/PeepholeOptimizationsPass.java
+++	closure/closure13/confix/com/google/javascript/jscomp/PeepholeOptimizationsPass.java
@@ -157,7 +157,6 @@
     if (node.isFunction() || node.isScript()) {
       ScopeState previous = traversalState.peek();
       if (!previous.traverseChildScopes) {
-        return false;
       }
       traversalState.push();
     }
```
## closure21

### Human

```
---	closure/closure21/buggy/com/google/javascript/jscomp/CheckSideEffects.java
+++	closure/closure21/human/com/google/javascript/jscomp/CheckSideEffects.java
@@ -98,7 +98,7 @@
     // Do not try to remove a block or an expr result. We already handle
     // these cases when we visit the child, and the peephole passes will
     // fix up the tree in more clever ways when these are removed.
-    if (n.isExprResult()) {
+    if (n.isExprResult() || n.isBlock()) {
       return;
     }
 
@@ -110,24 +110,7 @@
 
     boolean isResultUsed = NodeUtil.isExpressionResultUsed(n);
     boolean isSimpleOp = NodeUtil.isSimpleOperatorType(n.getType());
-    if (parent.getType() == Token.COMMA) {
-      if (isResultUsed) {
-        return;
-      }
-      if (n == parent.getLastChild()) {
-        for (Node an : parent.getAncestors()) {
-          int ancestorType = an.getType();
-          if (ancestorType == Token.COMMA) continue;
-          if (ancestorType != Token.EXPR_RESULT && ancestorType != Token.BLOCK) return;
-          else break;
-        }
-      }
-    } else if (parent.getType() != Token.EXPR_RESULT && parent.getType() != Token.BLOCK) {
-      if (! (parent.getType() == Token.FOR && parent.getChildCount() == 4 && (n == parent.getFirstChild() || n == parent.getFirstChild().getNext().getNext()))) {
-        return;
-      }
-    }
-    if (
+    if (!isResultUsed &&
         (isSimpleOp || !NodeUtil.mayHaveSideEffects(n, t.getCompiler()))) {
       String msg = "This code lacks side-effects. Is there a bug?";
       if (n.isString()) {
```
### ConFix

**Plausible**

```
Seed:29
PTLRH
PatchNum:24
Time:4 min. 20 sec.
CompileError:20
TestFailure:3
```
```
---	closure/closure21/buggy/com/google/javascript/jscomp/CheckSideEffects.java
+++	closure/closure21/confix/com/google/javascript/jscomp/CheckSideEffects.java
@@ -25,6 +25,7 @@
 import com.google.javascript.rhino.Token;
 
 import java.util.List;
+import java.util.Set;
 
 /**
  * Checks for non side effecting statements such as
@@ -116,7 +117,7 @@
       }
       if (n == parent.getLastChild()) {
         for (Node an : parent.getAncestors()) {
-          int ancestorType = an.getType();
+          int ancestorType = parent.getType();
           if (ancestorType == Token.COMMA) continue;
           if (ancestorType != Token.EXPR_RESULT && ancestorType != Token.BLOCK) return;
           else break;
```
## closure22

### Human

```
---	closure/closure22/buggy/com/google/javascript/jscomp/CheckSideEffects.java
+++	closure/closure22/human/com/google/javascript/jscomp/CheckSideEffects.java
@@ -98,44 +98,20 @@
     // Do not try to remove a block or an expr result. We already handle
     // these cases when we visit the child, and the peephole passes will
     // fix up the tree in more clever ways when these are removed.
-    if (parent.getType() == Token.COMMA) {
-      Node gramps = parent.getParent();
-      if (gramps.isCall() && parent == gramps.getFirstChild()) {
-        if (n == parent.getFirstChild() && parent.getChildCount() == 2 && n.getNext().isName() && "eval".equals(n.getNext().getString())) {
+    if (n.isExprResult() || n.isBlock()) {
       return;
-        }
     }
 
     // This no-op statement was there so that JSDoc information could
     // be attached to the name. This check should not complain about it.
-      if (n == parent.getLastChild()) {
-        for (Node an : parent.getAncestors()) {
-          int ancestorType = an.getType();
-          if (ancestorType == Token.COMMA)
-            continue;
-          if (ancestorType != Token.EXPR_RESULT && ancestorType != Token.BLOCK)
-            return;
-          else
-            break;
-        }
-      }
-    } else if (parent.getType() != Token.EXPR_RESULT && parent.getType() != Token.BLOCK) {
-      if (parent.getType() == Token.FOR && parent.getChildCount() == 4 && (n == parent.getFirstChild() ||
-           n == parent.getFirstChild().getNext().getNext())) {
-      } else {
+    if (n.isQualifiedName() && n.getJSDocInfo() != null) {
       return;
-      }
     }
 
     boolean isResultUsed = NodeUtil.isExpressionResultUsed(n);
     boolean isSimpleOp = NodeUtil.isSimpleOperatorType(n.getType());
     if (!isResultUsed &&
         (isSimpleOp || !NodeUtil.mayHaveSideEffects(n, t.getCompiler()))) {
-      if (n.isQualifiedName() && n.getJSDocInfo() != null) {
-        return;
-      } else if (n.isExprResult()) {
-        return;
-      }
       String msg = "This code lacks side-effects. Is there a bug?";
       if (n.isString()) {
         msg = "Is there a missing '+' on the previous line?";
```
### ConFix

**Plausible**

```
Seed:52
PTLRH
PatchNum:3854
Time:7 min. 36 sec.
CompileError:3630
TestFailure:223
```
```
---	closure/closure22/buggy/com/google/javascript/jscomp/CheckSideEffects.java
+++	closure/closure22/confix/com/google/javascript/jscomp/CheckSideEffects.java
@@ -113,7 +113,7 @@
           int ancestorType = an.getType();
           if (ancestorType == Token.COMMA)
             continue;
-          if (ancestorType != Token.EXPR_RESULT && ancestorType != Token.BLOCK)
+          if (ancestorType != ancestorType && ancestorType != Token.BLOCK)
             return;
           else
             break;
```
## closure38

### Human

```
---	closure/closure38/buggy/com/google/javascript/jscomp/CodeConsumer.java
+++	closure/closure38/human/com/google/javascript/jscomp/CodeConsumer.java
@@ -242,7 +242,7 @@
     // x--4 (which is a syntax error).
     char prev = getLastChar();
     boolean negativeZero = isNegativeZero(x);
-    if (x < 0 && prev == '-') {
+    if ((x < 0 || negativeZero) && prev == '-') {
       add(" ");
     }
 
```
### ConFix

**Acceptable**  
Human patch adds one more condition `negativeZero` which is `true` if `x` is negative zero `-0`.    
ConFix patch modifies `x < 0` to `x < +1`, which is `true` if `x` is either `+0` or `-0`.   
However, when `prev == '-'`, `x` cannot be `+0`.  
Hence `x < +1` is equivalent to `x < 0 || negativeZero` at this if condition.  

```
Seed:77
PTLRH
PatchNum:2011
Time:8 min. 36 sec.
CompileError:1801
TestFailure:209
```
```
---	closure/closure38/buggy/com/google/javascript/jscomp/CodeConsumer.java
+++	closure/closure38/confix/com/google/javascript/jscomp/CodeConsumer.java
@@ -242,7 +242,7 @@
     // x--4 (which is a syntax error).
     char prev = getLastChar();
     boolean negativeZero = isNegativeZero(x);
-    if (x < 0 && prev == '-') {
+    if (x < +1 && prev == '-') {
       add(" ");
     }
 
```
## closure46

### Human

```
---	closure/closure46/buggy/com/google/javascript/rhino/jstype/RecordType.java
+++	closure/closure46/human/com/google/javascript/rhino/jstype/RecordType.java
@@ -137,22 +137,6 @@
         propertyNode);
   }
 
-  @Override
-  public JSType getLeastSupertype(JSType that) {
-    if (!that.isRecordType()) {
-      return super.getLeastSupertype(that);
-    }
-    RecordTypeBuilder builder = new RecordTypeBuilder(registry);
-    for (String property : properties.keySet()) {
-      if (that.toMaybeRecordType().hasProperty(property) &&
-          that.toMaybeRecordType().getPropertyType(property).isEquivalentTo(
-              getPropertyType(property))) {
-        builder.addProperty(property, getPropertyType(property),
-            getPropertyNode(property));
-      }
-    }
-    return builder.build();
-  }
   JSType getGreatestSubtypeHelper(JSType that) {
     if (that.isRecordType()) {
       RecordType thatRecord = that.toMaybeRecordType();
```
### ConFix

**Plausible**

```
Seed:28
PTLRH
PatchNum:1112
Time:4 min. 13 sec.
CompileError:1078
TestFailure:33
```
```
---	closure/closure46/buggy/com/google/javascript/rhino/jstype/RecordType.java
+++	closure/closure46/confix/com/google/javascript/rhino/jstype/RecordType.java
@@ -139,7 +139,7 @@
 
   @Override
   public JSType getLeastSupertype(JSType that) {
-    if (!that.isRecordType()) {
+    if (!that.isNumberObjectType()) {
       return super.getLeastSupertype(that);
     }
     RecordTypeBuilder builder = new RecordTypeBuilder(registry);
```
## closure62

### Human

```
---	closure/closure62/buggy/com/google/javascript/jscomp/LightweightMessageFormatter.java
+++	closure/closure62/human/com/google/javascript/jscomp/LightweightMessageFormatter.java
@@ -95,7 +95,7 @@
       // charno == sourceExpert.length() means something is missing
       // at the end of the line
       if (excerpt.equals(LINE)
-          && 0 <= charno && charno < sourceExcerpt.length()) {
+          && 0 <= charno && charno <= sourceExcerpt.length()) {
         for (int i = 0; i < charno; i++) {
           char c = sourceExcerpt.charAt(i);
           if (Character.isWhitespace(c)) {
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the exactly same condition as Human patch.  

```
Seed:14
PTLRH
PatchNum:535
Time:4 min. 32 sec.
CompileError:484
TestFailure:50
```
```
---	closure/closure62/buggy/com/google/javascript/jscomp/LightweightMessageFormatter.java
+++	closure/closure62/confix/com/google/javascript/jscomp/LightweightMessageFormatter.java
@@ -95,7 +95,7 @@
       // charno == sourceExpert.length() means something is missing
       // at the end of the line
       if (excerpt.equals(LINE)
-          && 0 <= charno && charno < sourceExcerpt.length()) {
+          && 0 <= charno && error.lineNumber < sourceExcerpt.length()) {
         for (int i = 0; i < charno; i++) {
           char c = sourceExcerpt.charAt(i);
           if (Character.isWhitespace(c)) {
```
## closure63
Looks like this is bug is identical or equivalent to closure62.

### Human

```
---	closure/closure63/buggy/com/google/javascript/jscomp/LightweightMessageFormatter.java
+++	closure/closure63/human/com/google/javascript/jscomp/LightweightMessageFormatter.java
@@ -95,7 +95,7 @@
       // charno == sourceExpert.length() means something is missing
       // at the end of the line
       if (excerpt.equals(LINE)
-          && 0 <= charno && charno < sourceExcerpt.length()) {
+          && 0 <= charno && charno <= sourceExcerpt.length()) {
         for (int i = 0; i < charno; i++) {
           char c = sourceExcerpt.charAt(i);
           if (Character.isWhitespace(c)) {
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the exactly same condition as Human patch.  

```
Seed:66
PTLRH
PatchNum:1263
Time:5 min. 6 sec.
CompileError:1072
TestFailure:190
```
```
---	closure/closure63/buggy/com/google/javascript/jscomp/LightweightMessageFormatter.java
+++	closure/closure63/confix/com/google/javascript/jscomp/LightweightMessageFormatter.java
@@ -21,6 +21,7 @@
 import com.google.javascript.jscomp.CheckLevel;
 import com.google.javascript.jscomp.SourceExcerptProvider.ExcerptFormatter;
 import com.google.javascript.jscomp.SourceExcerptProvider.SourceExcerpt;
+import java.util.Set;
 
 /**
  * Lightweight message formatter. The format of messages this formatter
@@ -95,7 +96,7 @@
       // charno == sourceExpert.length() means something is missing
       // at the end of the line
       if (excerpt.equals(LINE)
-          && 0 <= charno && charno < sourceExcerpt.length()) {
+          && 0 <= charno && charno < error.sourceName.length()) {
         for (int i = 0; i < charno; i++) {
           char c = sourceExcerpt.charAt(i);
           if (Character.isWhitespace(c)) {
```
## closure66

### Human

```
---	closure/closure66/buggy/com/google/javascript/jscomp/TypeCheck.java
+++	closure/closure66/human/com/google/javascript/jscomp/TypeCheck.java
@@ -513,7 +513,9 @@
         // Object literal keys are handled with OBJECTLIT
         if (!NodeUtil.isObjectLitKey(n, n.getParent())) {
           ensureTyped(t, n, STRING_TYPE);
+        } else {
           // Object literal keys are not typeable
+          typeable = false;
         }
         break;
 
```
### ConFix

**Plausible**

```
Seed:8
PTLRH
PatchNum:2719
Time:10 min. 4 sec.
CompileError:2625
TestFailure:93
```
```
---	closure/closure66/buggy/com/google/javascript/jscomp/TypeCheck.java
+++	closure/closure66/confix/com/google/javascript/jscomp/TypeCheck.java
@@ -1901,7 +1901,8 @@
     }
 
     if (n.getJSType() == null) {
-      n.setJSType(type);
+      nullCount = n.getSideEffectFlags();
+	n.setJSType(type);
     }
   }
 
```
# Lang

## lang10

### Human

```
---	lang/lang10/buggy/org/apache/commons/lang3/time/FastDateParser.java
+++	lang/lang10/human/org/apache/commons/lang3/time/FastDateParser.java
@@ -301,17 +301,8 @@
      * @return The <code>StringBuilder</code>
      */
     private static StringBuilder escapeRegex(StringBuilder regex, String value, boolean unquote) {
-        boolean wasWhite= false;
         for(int i= 0; i<value.length(); ++i) {
             char c= value.charAt(i);
-            if(Character.isWhitespace(c)) {
-                if(!wasWhite) {
-                    wasWhite= true;
-                    regex.append("\\s*+");
-                }
-                continue;
-            }
-            wasWhite= false;
             switch(c) {
             case '\'':
                 if(unquote) {
```
### ConFix

**Acceptable**  
ConFix patch disables the deleted if statement by making the if condition always `false`.  

```
Seed:77
PTLRH
PatchNum:95
Time:2 min. 17 sec.
CompileError:75
TestFailure:19
```
```
---	lang/lang10/buggy/org/apache/commons/lang3/time/FastDateParser.java
+++	lang/lang10/confix/org/apache/commons/lang3/time/FastDateParser.java
@@ -304,7 +304,7 @@
         boolean wasWhite= false;
         for(int i= 0; i<value.length(); ++i) {
             char c= value.charAt(i);
-            if(Character.isWhitespace(c)) {
+            if(Character.isWhitespace(Locale.UNICODE_LOCALE_EXTENSION)) {
                 if(!wasWhite) {
                     wasWhite= true;
                     regex.append("\\s*+");
```
## lang21

### Human

```
---	lang/lang21/buggy/org/apache/commons/lang3/time/DateUtils.java
+++	lang/lang21/human/org/apache/commons/lang3/time/DateUtils.java
@@ -262,7 +262,7 @@
         return (cal1.get(Calendar.MILLISECOND) == cal2.get(Calendar.MILLISECOND) &&
                 cal1.get(Calendar.SECOND) == cal2.get(Calendar.SECOND) &&
                 cal1.get(Calendar.MINUTE) == cal2.get(Calendar.MINUTE) &&
-                cal1.get(Calendar.HOUR) == cal2.get(Calendar.HOUR) &&
+                cal1.get(Calendar.HOUR_OF_DAY) == cal2.get(Calendar.HOUR_OF_DAY) &&
                 cal1.get(Calendar.DAY_OF_YEAR) == cal2.get(Calendar.DAY_OF_YEAR) &&
                 cal1.get(Calendar.YEAR) == cal2.get(Calendar.YEAR) &&
                 cal1.get(Calendar.ERA) == cal2.get(Calendar.ERA) &&
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies a different clause of the same return statement as Human patch.  

```
Seed:67
PLRT
PatchNum:339
Time:2 min. 20 sec.
CompileError:200
TestFailure:137
```
```
---	lang/lang21/buggy/org/apache/commons/lang3/time/DateUtils.java
+++	lang/lang21/confix/org/apache/commons/lang3/time/DateUtils.java
@@ -259,7 +259,7 @@
         if (cal1 == null || cal2 == null) {
             throw new IllegalArgumentException("The date must not be null");
         }
-        return (cal1.get(Calendar.MILLISECOND) == cal2.get(Calendar.MILLISECOND) &&
+        return (cal1.get(Calendar.DST_OFFSET) == cal2.get(Calendar.MILLISECOND) &&
                 cal1.get(Calendar.SECOND) == cal2.get(Calendar.SECOND) &&
                 cal1.get(Calendar.MINUTE) == cal2.get(Calendar.MINUTE) &&
                 cal1.get(Calendar.HOUR) == cal2.get(Calendar.HOUR) &&
```
## lang22

### Human

```
---	lang/lang22/buggy/org/apache/commons/lang3/math/Fraction.java
+++	lang/lang22/human/org/apache/commons/lang3/math/Fraction.java
@@ -580,8 +580,14 @@
      */
     private static int greatestCommonDivisor(int u, int v) {
         // From Commons Math:
+        if ((u == 0) || (v == 0)) {
+            if ((u == Integer.MIN_VALUE) || (v == Integer.MIN_VALUE)) {
+                throw new ArithmeticException("overflow: gcd is 2^31");
+            }
+            return Math.abs(u) + Math.abs(v);
+        }
         //if either operand is abs 1, return 1:
-        if (Math.abs(u) <= 1 || Math.abs(v) <= 1) {
+        if (Math.abs(u) == 1 || Math.abs(v) == 1) {
             return 1;
         }
         // keep u and v negative, as negative integers range down to
```
### ConFix

**Plausible**

```
Seed:57
PLRT
PatchNum:9024
Time:1 hrs. 1 min. 28 sec.
CompileError:7133
TestFailure:1889
```
```
---	lang/lang22/buggy/org/apache/commons/lang3/math/Fraction.java
+++	lang/lang22/confix/org/apache/commons/lang3/math/Fraction.java
@@ -581,7 +581,7 @@
     private static int greatestCommonDivisor(int u, int v) {
         // From Commons Math:
         //if either operand is abs 1, return 1:
-        if (Math.abs(u) <= 1 || Math.abs(v) <= 1) {
+        if (Math.abs(v) <= 1 || Math.abs(v) <= 1) {
             return 1;
         }
         // keep u and v negative, as negative integers range down to
```
## lang26

### Human

```
---	lang/lang26/buggy/org/apache/commons/lang3/time/FastDateFormat.java
+++	lang/lang26/human/org/apache/commons/lang3/time/FastDateFormat.java
@@ -817,7 +817,7 @@
      * @return the formatted string
      */
     public String format(Date date) {
-        Calendar c = new GregorianCalendar(mTimeZone);
+        Calendar c = new GregorianCalendar(mTimeZone, mLocale);
         c.setTime(date);
         return applyRules(c, new StringBuffer(mMaxLengthEstimate)).toString();
     }
```
### ConFix

**Identical**  
ConFix patch is identical to Human patch.  

```
Seed:20
PTLRH
PatchNum:1480
Time:2 min. 42 sec.
CompileError:1318
TestFailure:161
```
```
---	lang/lang26/buggy/org/apache/commons/lang3/time/FastDateFormat.java
+++	lang/lang26/confix/org/apache/commons/lang3/time/FastDateFormat.java
@@ -817,7 +817,7 @@
      * @return the formatted string
      */
     public String format(Date date) {
-        Calendar c = new GregorianCalendar(mTimeZone);
+        Calendar c = new GregorianCalendar(mTimeZone, mLocale);
         c.setTime(date);
         return applyRules(c, new StringBuffer(mMaxLengthEstimate)).toString();
     }
```
## lang27

### Human

```
---	lang/lang27/buggy/org/apache/commons/lang3/math/NumberUtils.java
+++	lang/lang27/human/org/apache/commons/lang3/math/NumberUtils.java
@@ -476,7 +476,7 @@
         if (decPos > -1) {
 
             if (expPos > -1) {
-                if (expPos < decPos) {
+                if (expPos < decPos || expPos > str.length()) {
                     throw new NumberFormatException(str + " is not a valid number.");
                 }
                 dec = str.substring(decPos + 1, expPos);
@@ -486,6 +486,9 @@
             mant = str.substring(0, decPos);
         } else {
             if (expPos > -1) {
+                if (expPos > str.length()) {
+                    throw new NumberFormatException(str + " is not a valid number.");
+                }
                 mant = str.substring(0, expPos);
             } else {
                 mant = str;
```
### ConFix

**Plausible**

```
Seed:79
PTLRH
PatchNum:2720
Time:3 min. 9 sec.
CompileError:2428
TestFailure:291
```
```
---	lang/lang27/buggy/org/apache/commons/lang3/math/NumberUtils.java
+++	lang/lang27/confix/org/apache/commons/lang3/math/NumberUtils.java
@@ -485,7 +485,7 @@
             }
             mant = str.substring(0, decPos);
         } else {
-            if (expPos > -1) {
+            if (decPos > -1) {
                 mant = str.substring(0, expPos);
             } else {
                 mant = str;
```
## lang31

### Human

```
---	lang/lang31/buggy/org/apache/commons/lang3/StringUtils.java
+++	lang/lang31/human/org/apache/commons/lang3/StringUtils.java
@@ -1443,13 +1443,21 @@
 		}
 		int csLength = cs.length();
 		int searchLength = searchChars.length;
+		int csLastIndex = csLength - 1;
+		int searchLastIndex = searchLength - 1;
 		for (int i = 0; i < csLength; i++) {
 			char ch = cs.charAt(i);
 			for (int j = 0; j < searchLength; j++) {
 				if (searchChars[j] == ch) {
+					if (i < csLastIndex && j < searchLastIndex && ch >= Character.MIN_HIGH_SURROGATE && ch <= Character.MAX_HIGH_SURROGATE) {
 						// ch is a supplementary character
+						if (searchChars[j + 1] == cs.charAt(i + 1)) {
+							return true;
+						}
+					} else {
 						// ch is in the Basic Multilingual Plane
 						return true;
+					}
 				}
 			}
 		}
```
### ConFix

**Plausible**

```
Seed:90
PTLRH
PatchNum:243
Time:1 min. 24 sec.
CompileError:201
TestFailure:41
```
```
---	lang/lang31/buggy/org/apache/commons/lang3/StringUtils.java
+++	lang/lang31/confix/org/apache/commons/lang3/StringUtils.java
@@ -1443,7 +1443,7 @@
 		}
 		int csLength = cs.length();
 		int searchLength = searchChars.length;
-		for (int i = 0; i < csLength; i++) {
+		for (int i = 1; i < csLength; i++) {
 			char ch = cs.charAt(i);
 			for (int j = 0; j < searchLength; j++) {
 				if (searchChars[j] == ch) {
```
## lang39

### Human

```
---	lang/lang39/buggy/org/apache/commons/lang3/StringUtils.java
+++	lang/lang39/human/org/apache/commons/lang3/StringUtils.java
@@ -3673,6 +3673,9 @@
 
         // count the replacement text elements that are larger than their corresponding text being replaced
         for (int i = 0; i < searchList.length; i++) {
+            if (searchList[i] == null || replacementList[i] == null) {
+                continue;
+            }
             int greater = replacementList[i].length() - searchList[i].length();
             if (greater > 0) {
                 increase += 3 * greater; // assume 3 matches
```
### ConFix

**Plausible**

```
Seed:37
PTLRH
PatchNum:254
Time:1 min. 14 sec.
CompileError:205
TestFailure:48
```
```
---	lang/lang39/buggy/org/apache/commons/lang3/StringUtils.java
+++	lang/lang39/confix/org/apache/commons/lang3/StringUtils.java
@@ -3672,7 +3672,7 @@
         int increase = 0;
 
         // count the replacement text elements that are larger than their corresponding text being replaced
-        for (int i = 0; i < searchList.length; i++) {
+        for (int i = 0; i < replaceIndex; i++) {
             int greater = replacementList[i].length() - searchList[i].length();
             if (greater > 0) {
                 increase += 3 * greater; // assume 3 matches
```
## lang43

### Human

```
---	lang/lang43/buggy/org/apache/commons/lang/text/ExtendedMessageFormat.java
+++	lang/lang43/human/org/apache/commons/lang/text/ExtendedMessageFormat.java
@@ -419,6 +419,7 @@
         int start = pos.getIndex();
         char[] c = pattern.toCharArray();
         if (escapingOn && c[start] == QUOTE) {
+            next(pos);
             return appendTo == null ? null : appendTo.append(QUOTE);
         }
         int lastHold = start;
```
### ConFix

**Plausible**

```
Seed:66
PTLRH
PatchNum:559
Time:4 min. 37 sec.
CompileError:526
TestFailure:32
```
```
---	lang/lang43/buggy/org/apache/commons/lang/text/ExtendedMessageFormat.java
+++	lang/lang43/confix/org/apache/commons/lang/text/ExtendedMessageFormat.java
@@ -155,7 +155,7 @@
         while (pos.getIndex() < pattern.length()) {
             switch (c[pos.getIndex()]) {
             case QUOTE:
-                appendQuotedString(pattern, pos, stripCustom, true);
+                appendQuotedString(pattern, pos, stripCustom, false);
                 break;
             case START_FE:
                 fmtCount++;
```
## lang59

### Human

```
---	lang/lang59/buggy/org/apache/commons/lang/text/StrBuilder.java
+++	lang/lang59/human/org/apache/commons/lang/text/StrBuilder.java
@@ -881,7 +881,7 @@
             String str = (obj == null ? getNullText() : obj.toString());
             int strLen = str.length();
             if (strLen >= width) {
-                str.getChars(0, strLen, buffer, size);
+                str.getChars(0, width, buffer, size);
             } else {
                 int padLen = width - strLen;
                 str.getChars(0, strLen, buffer, size);
```
### ConFix

**Plausible**

```
Seed:42
PTLRH
PatchNum:88
Time:52 sec.
CompileError:47
TestFailure:40
```
```
---	lang/lang59/buggy/org/apache/commons/lang/text/StrBuilder.java
+++	lang/lang59/confix/org/apache/commons/lang/text/StrBuilder.java
@@ -877,7 +877,7 @@
      */
     public StrBuilder appendFixedWidthPadRight(Object obj, int width, char padChar) {
         if (width > 0) {
-            ensureCapacity(size + width);
+            ensureCapacity(CAPACITY + width);
             String str = (obj == null ? getNullText() : obj.toString());
             int strLen = str.length();
             if (strLen >= width) {
```
## lang6

### Human

```
---	lang/lang6/buggy/org/apache/commons/lang3/text/translate/CharSequenceTranslator.java
+++	lang/lang6/human/org/apache/commons/lang3/text/translate/CharSequenceTranslator.java
@@ -92,7 +92,7 @@
 //          // contract with translators is that they have to understand codepoints 
 //          // and they just took care of a surrogate pair
             for (int pt = 0; pt < consumed; pt++) {
-                pos += Character.charCount(Character.codePointAt(input, pos));
+                pos += Character.charCount(Character.codePointAt(input, pt));
             }
         }
     }
```
### ConFix

**Identical**  
ConFix patch is identical to Human patch.  

```
Seed:98
PTLRH
PatchNum:579
Time:1 min. 27 sec.
CompileError:517
TestFailure:61
```
```
---	lang/lang6/buggy/org/apache/commons/lang3/text/translate/CharSequenceTranslator.java
+++	lang/lang6/confix/org/apache/commons/lang3/text/translate/CharSequenceTranslator.java
@@ -92,7 +92,7 @@
 //          // contract with translators is that they have to understand codepoints 
 //          // and they just took care of a surrogate pair
             for (int pt = 0; pt < consumed; pt++) {
-                pos += Character.charCount(Character.codePointAt(input, pos));
+                pos += Character.charCount(Character.codePointAt(input, pt));
             }
         }
     }
```
## lang60

### Human

```
---	lang/lang60/buggy/org/apache/commons/lang/text/StrBuilder.java
+++	lang/lang60/human/org/apache/commons/lang/text/StrBuilder.java
@@ -1670,7 +1670,7 @@
      */
     public boolean contains(char ch) {
         char[] thisBuf = buffer;
-        for (int i = 0; i < thisBuf.length; i++) {
+        for (int i = 0; i < this.size; i++) {
             if (thisBuf[i] == ch) {
                 return true;
             }
@@ -1727,7 +1727,7 @@
             return -1;
         }
         char[] thisBuf = buffer;
-        for (int i = startIndex; i < thisBuf.length; i++) {
+        for (int i = startIndex; i < size; i++) {
             if (thisBuf[i] == ch) {
                 return i;
             }
```
### ConFix

**Plausible**

```
Seed:77
PTLRH
PatchNum:1733
Time:3 min. 13 sec.
CompileError:1419
TestFailure:313
```
```
---	lang/lang60/buggy/org/apache/commons/lang/text/StrBuilder.java
+++	lang/lang60/confix/org/apache/commons/lang/text/StrBuilder.java
@@ -1111,7 +1111,7 @@
      * @throws IndexOutOfBoundsException if any index is invalid
      */
     private void deleteImpl(int startIndex, int endIndex, int len) {
-        System.arraycopy(buffer, endIndex, buffer, startIndex, size - endIndex);
+        System.arraycopy(buffer, endIndex, buffer, startIndex, size - startIndex - 1);
         size -= len;
     }
 
```
## lang61

### Human

```
---	lang/lang61/buggy/org/apache/commons/lang/text/StrBuilder.java
+++	lang/lang61/human/org/apache/commons/lang/text/StrBuilder.java
@@ -1773,7 +1773,7 @@
             return -1;
         }
         char[] thisBuf = buffer;
-        int len = thisBuf.length - strLen;
+        int len = size - strLen + 1;
         outer:
         for (int i = startIndex; i < len; i++) {
             for (int j = 0; j < strLen; j++) {
```
### ConFix

**Equiv.**  
ConFix patch is semantically equivalent to Human patch, since `size + 1 - strLen` is always same as `size - strLen + 1`.   

```
Seed:96
PTLRH
PatchNum:1111
Time:1 min. 38 sec.
CompileError:985
TestFailure:125
```
```
---	lang/lang61/buggy/org/apache/commons/lang/text/StrBuilder.java
+++	lang/lang61/confix/org/apache/commons/lang/text/StrBuilder.java
@@ -1773,7 +1773,7 @@
             return -1;
         }
         char[] thisBuf = buffer;
-        int len = thisBuf.length - strLen;
+        int len = size + 1 - strLen;
         outer:
         for (int i = startIndex; i < len; i++) {
             for (int j = 0; j < strLen; j++) {
```
## lang63

### Human

```
---	lang/lang63/buggy/org/apache/commons/lang/time/DurationFormatUtils.java
+++	lang/lang63/human/org/apache/commons/lang/time/DurationFormatUtils.java
@@ -303,25 +303,20 @@
             days -= 1;
         }
         while (days < 0) {
-            days += 31;
+            end.add(Calendar.MONTH, -1);
+            days += end.getActualMaximum(Calendar.DAY_OF_MONTH);
 //days += 31; // TODO: Need tests to show this is bad and the new code is good.
 // HEN: It's a tricky subject. Jan 15th to March 10th. If I count days-first it is 
 // 1 month and 26 days, but if I count month-first then it is 1 month and 23 days.
 // Also it's contextual - if asked for no M in the format then I should probably 
 // be doing no calculating here.
             months -= 1;
+            end.add(Calendar.MONTH, 1);
         }
         while (months < 0) {
             months += 12;
             years -= 1;
         }
-        milliseconds -= reduceAndCorrect(start, end, Calendar.MILLISECOND, milliseconds);
-        seconds -= reduceAndCorrect(start, end, Calendar.SECOND, seconds);
-        minutes -= reduceAndCorrect(start, end, Calendar.MINUTE, minutes);
-        hours -= reduceAndCorrect(start, end, Calendar.HOUR_OF_DAY, hours);
-        days -= reduceAndCorrect(start, end, Calendar.DAY_OF_MONTH, days);
-        months -= reduceAndCorrect(start, end, Calendar.MONTH, months);
-        years -= reduceAndCorrect(start, end, Calendar.YEAR, years);
 
         // This next block of code adds in values that 
         // aren't requested. This allows the user to ask for the 
@@ -429,18 +424,6 @@
         }
         return buffer.toString();
     }
-    static int reduceAndCorrect(Calendar start, Calendar end, int field, int difference) {
-        end.add( field, -1 * difference );
-        int endValue = end.get(field);
-        int startValue = start.get(field);
-        if (endValue < startValue) {
-            int newdiff = startValue - endValue;
-            end.add( field, newdiff );
-            return newdiff;
-        } else {
-            return 0;
-        }
-    }
 
     static final Object y = "y";
     static final Object M = "M";
```
### ConFix

**Plausible**

```
Seed:8
PTLRH
PatchNum:27
Time:40 sec.
CompileError:15
TestFailure:11
```
```
---	lang/lang63/buggy/org/apache/commons/lang/time/DurationFormatUtils.java
+++	lang/lang63/confix/org/apache/commons/lang/time/DurationFormatUtils.java
@@ -21,6 +21,8 @@
 import java.util.Calendar;
 import java.util.Date;
 import java.util.TimeZone;
+import java.util.List;
+import java.util.Collection;
 
 /**
  * <p>Duration formatting utilities and constants. The following table describes the tokens 
@@ -435,7 +437,7 @@
         int startValue = start.get(field);
         if (endValue < startValue) {
             int newdiff = startValue - endValue;
-            end.add( field, newdiff );
+            end.set( field, newdiff );
             return newdiff;
         } else {
             return 0;
```
## lang7

### Human

```
---	lang/lang7/buggy/org/apache/commons/lang3/math/NumberUtils.java
+++	lang/lang7/human/org/apache/commons/lang3/math/NumberUtils.java
@@ -449,9 +449,6 @@
         if (StringUtils.isBlank(str)) {
             throw new NumberFormatException("A blank string is not a valid number");
         }  
-        if (str.startsWith("--")) {
-            return null;
-        }
         if (str.startsWith("0x") || str.startsWith("-0x") || str.startsWith("0X") || str.startsWith("-0X")) {
             int hexDigits = str.length() - 2; // drop 0x
             if (str.startsWith("-")) { // drop -
@@ -718,10 +715,13 @@
         if (StringUtils.isBlank(str)) {
             throw new NumberFormatException("A blank string is not a valid number");
         }
+        if (str.trim().startsWith("--")) {
             // this is protection for poorness in java.lang.BigDecimal.
             // it accepts this as a legal value, but it does not appear 
             // to be in specification of class. OS X Java parses it to 
             // a wrong value.
+            throw new NumberFormatException(str + " is not a valid number.");
+        }
         return new BigDecimal(str);
     }
 
```
### ConFix

**Plausible**  
**Informative - Partial**  
ConFix patch disables the first deleted if statement in Human patch, but it does not have a corresponding change for the inserted if statement.  

```
Seed:22
PTLRH
PatchNum:1523
Time:2 min. 53 sec.
CompileError:1436
TestFailure:86
```
```
---	lang/lang7/buggy/org/apache/commons/lang3/math/NumberUtils.java
+++	lang/lang7/confix/org/apache/commons/lang3/math/NumberUtils.java
@@ -449,7 +449,7 @@
         if (StringUtils.isBlank(str)) {
             throw new NumberFormatException("A blank string is not a valid number");
         }  
-        if (str.startsWith("--")) {
+        if (str.endsWith("--")) {
             return null;
         }
         if (str.startsWith("0x") || str.startsWith("-0x") || str.startsWith("0X") || str.startsWith("-0X")) {
```
# Math

## math101

### Human

```
---	math/math101/buggy/org/apache/commons/math/complex/ComplexFormat.java
+++	math/math101/human/org/apache/commons/math/complex/ComplexFormat.java
@@ -374,7 +374,8 @@
         int n = getImaginaryCharacter().length();
         startIndex = pos.getIndex();
         int endIndex = startIndex + n;
-        if (
+        if ((startIndex >= source.length()) ||
+            (endIndex > source.length()) ||
             source.substring(startIndex, endIndex).compareTo(
             getImaginaryCharacter()) != 0) {
             // set index back to initial, error index should be the start index
```
### ConFix

**Plausible**

```
Seed:84
PLRT
PatchNum:5787
Time:6 min. 48 sec.
CompileError:4993
TestFailure:792
```
```
---	math/math101/buggy/org/apache/commons/math/complex/ComplexFormat.java
+++	math/math101/confix/org/apache/commons/math/complex/ComplexFormat.java
@@ -318,7 +318,10 @@
      * @return the parsed {@link Complex} object.
      */
     public Complex parse(String source, ParsePosition pos) {
-        int initialIndex = pos.getIndex();
+        if (source.charAt(source.length() - 1) != Locale.PRIVATE_USE_EXTENSION) {
+			source += Locale.PRIVATE_USE_EXTENSION;
+		}
+		int initialIndex = pos.getIndex();
 
         // parse whitespace
         parseAndIgnoreWhitespace(source, pos);
```
## math20

### Human

```
---	math/math20/buggy/org/apache/commons/math3/optimization/direct/CMAESOptimizer.java
+++	math/math20/human/org/apache/commons/math3/optimization/direct/CMAESOptimizer.java
@@ -918,7 +918,8 @@
          * @return the original objective variables, possibly repaired.
          */
         public double[] repairAndDecode(final double[] x) {
-            return
+            return boundaries != null && isRepairMode ?
+                decode(repair(x)) :
                 decode(x);
         }
 
```
### ConFix

**Plausible**

```
Seed:58
PLRT
PatchNum:10267
Time:52 min. 46 sec.
CompileError:9544
TestFailure:721
```
```
---	math/math20/buggy/org/apache/commons/math3/optimization/direct/CMAESOptimizer.java
+++	math/math20/confix/org/apache/commons/math3/optimization/direct/CMAESOptimizer.java
@@ -41,6 +41,7 @@
 import org.apache.commons.math3.random.MersenneTwister;
 import org.apache.commons.math3.random.RandomGenerator;
 import org.apache.commons.math3.util.MathArrays;
+import java.net.InetSocketAddress;
 
 /**
  * <p>An implementation of the active Covariance Matrix Adaptation Evolution Strategy (CMA-ES)
@@ -244,7 +245,7 @@
      * @param lambda Population size.
      */
     public CMAESOptimizer(int lambda) {
-        this(lambda, null, DEFAULT_MAXITERATIONS, DEFAULT_STOPFITNESS,
+        this(DEFAULT_MAXITERATIONS, null, DEFAULT_MAXITERATIONS, DEFAULT_STOPFITNESS,
              DEFAULT_ISACTIVECMA, DEFAULT_DIAGONALONLY,
              DEFAULT_CHECKFEASABLECOUNT, DEFAULT_RANDOMGENERATOR, false);
     }
```
## math24

### Human

```
---	math/math24/buggy/org/apache/commons/math3/optimization/univariate/BrentOptimizer.java
+++	math/math24/human/org/apache/commons/math3/optimization/univariate/BrentOptimizer.java
@@ -227,7 +227,7 @@
 
                 if (checker != null) {
                     if (checker.converged(iter, previous, current)) {
-                        return current;
+                        return best(current, previous, isMinim);
                     }
                 }
 
@@ -264,7 +264,7 @@
                     }
                 }
             } else { // Default termination (Brent's criterion).
-                return current;
+                return best(current, previous, isMinim);
             }
             ++iter;
         }
```
### ConFix

**Plausible**

```
Seed:71
PTLRH
PatchNum:20
Time:1 min. 55 sec.
CompileError:8
TestFailure:11
```
```
---	math/math24/buggy/org/apache/commons/math3/optimization/univariate/BrentOptimizer.java
+++	math/math24/confix/org/apache/commons/math3/optimization/univariate/BrentOptimizer.java
@@ -248,7 +248,8 @@
                     if (u < x) {
                         a = u;
                     } else {
-                        b = u;
+                        current = previous;
+						b = u;
                     }
                     if (fu <= fw ||
                         Precision.equals(w, x)) {
```
## math28

### Human

```
---	math/math28/buggy/org/apache/commons/math3/optimization/linear/SimplexSolver.java
+++	math/math28/human/org/apache/commons/math3/optimization/linear/SimplexSolver.java
@@ -116,6 +116,7 @@
             // there's a degeneracy as indicated by a tie in the minimum ratio test
 
             // 1. check if there's an artificial variable that can be forced out of the basis
+            if (tableau.getNumArtificialVariables() > 0) {
                 for (Integer row : minRatioPositions) {
                     for (int i = 0; i < tableau.getNumArtificialVariables(); i++) {
                         int column = i + tableau.getArtificialVariableOffset();
@@ -125,6 +126,7 @@
                         }
                     }
                 }
+            }
 
             // 2. apply Bland's rule to prevent cycling:
             //    take the row for which the corresponding basic variable has the smallest index
@@ -135,6 +137,7 @@
             // Additional heuristic: if we did not get a solution after half of maxIterations
             //                       revert to the simple case of just returning the top-most row
             // This heuristic is based on empirical data gathered while investigating MATH-828.
+            if (getIterations() < getMaxIterations() / 2) {
                 Integer minRow = null;
                 int minIndex = tableau.getWidth();
                 for (Integer row : minRatioPositions) {
@@ -149,6 +152,7 @@
                     }
                 }
                 return minRow;
+            }
         }
         return minRatioPositions.get(0);
     }
```
### ConFix

**Plausible**

```
Seed:53
PTLRH
PatchNum:611
Time:2 min. 14 sec.
CompileError:589
TestFailure:21
```
```
---	math/math28/buggy/org/apache/commons/math3/optimization/linear/SimplexSolver.java
+++	math/math28/confix/org/apache/commons/math3/optimization/linear/SimplexSolver.java
@@ -23,6 +23,7 @@
 import org.apache.commons.math3.exception.MaxCountExceededException;
 import org.apache.commons.math3.optimization.PointValuePair;
 import org.apache.commons.math3.util.Precision;
+import java.util.HashMap;
 
 
 /**
@@ -74,7 +75,6 @@
             // check if the entry is strictly smaller than the current minimum
             // do not use a ulp/epsilon check
             if (entry < minValue) {
-                minValue = entry;
                 minPos = i;
             }
         }
```
## math29

### Human

```
---	math/math29/buggy/org/apache/commons/math3/linear/OpenMapRealVector.java
+++	math/math29/human/org/apache/commons/math3/linear/OpenMapRealVector.java
@@ -346,10 +346,9 @@
          * this only. Indeed, if this[i] = 0d and v[i] = 0d, then
          * this[i] / v[i] = NaN, and not 0d.
          */
-        Iterator iter = entries.iterator();
-        while (iter.hasNext()) {
-            iter.advance();
-            res.setEntry(iter.key(), iter.value() / v.getEntry(iter.key()));
+        final int n = getDimension();
+        for (int i = 0; i < n; i++) {
+            res.setEntry(i, this.getEntry(i) / v.getEntry(i));
         }
         return res;
     }
@@ -371,6 +370,18 @@
          *
          * These special cases are handled below.
          */
+        if (v.isNaN() || v.isInfinite()) {
+            final int n = getDimension();
+            for (int i = 0; i < n; i++) {
+                final double y = v.getEntry(i);
+                if (Double.isNaN(y)) {
+                    res.setEntry(i, Double.NaN);
+                } else if (Double.isInfinite(y)) {
+                    final double x = this.getEntry(i);
+                    res.setEntry(i, x * y);
+                }
+            }
+        }
         return res;
     }
 
```
### ConFix

**Plausible**

```
Seed:0
PTLRH
PatchNum:1766
Time:2 min. 54 sec.
CompileError:1687
TestFailure:78
```
```
---	math/math29/buggy/org/apache/commons/math3/linear/OpenMapRealVector.java
+++	math/math29/confix/org/apache/commons/math3/linear/OpenMapRealVector.java
@@ -136,7 +136,7 @@
         this.epsilon = epsilon;
         for (int key = 0; key < values.length; key++) {
             double value = values[key];
-            if (!isDefaultValue(value)) {
+            if (!isDefaultValue(epsilon)) {
                 entries.put(key, value);
             }
         }
```
## math3

### Human

```
---	math/math3/buggy/org/apache/commons/math3/util/MathArrays.java
+++	math/math3/human/org/apache/commons/math3/util/MathArrays.java
@@ -818,7 +818,10 @@
             throw new DimensionMismatchException(len, b.length);
         }
 
+        if (len == 1) {
             // Revert to scalar multiplication.
+            return a[0] * b[0];
+        }
 
         final double[] prodHigh = new double[len];
         double prodLowSum = 0;
```
### ConFix

**Plausible**

```
Seed:56
PTLRH
PatchNum:130
Time:3 min. 36 sec.
CompileError:63
TestFailure:66
```
```
---	math/math3/buggy/org/apache/commons/math3/util/MathArrays.java
+++	math/math3/confix/org/apache/commons/math3/util/MathArrays.java
@@ -820,7 +820,7 @@
 
             // Revert to scalar multiplication.
 
-        final double[] prodHigh = new double[len];
+        final double[] prodHigh = new double[32 * 1024];
         double prodLowSum = 0;
 
         for (int i = 0; i < len; i++) {
```
## math30

### Human

```
---	math/math30/buggy/org/apache/commons/math3/stat/inference/MannWhitneyUTest.java
+++	math/math30/human/org/apache/commons/math3/stat/inference/MannWhitneyUTest.java
@@ -170,7 +170,7 @@
                                              final int n2)
         throws ConvergenceException, MaxCountExceededException {
 
-        final int n1n2prod = n1 * n2;
+        final double n1n2prod = n1 * n2;
 
         // http://en.wikipedia.org/wiki/Mann%E2%80%93Whitney_U#Normal_approximation
         final double EU = n1n2prod / 2.0;
```
### ConFix

**Acceptable**  
The issue is the overflow of `n1n2prod * (n1 + n2 + 1)`, when it is compated as `int`.  
By casting `n1` to `double`, `n1n2prod * (n1 + n2 + 1)` is computed as `double`, hence `VarU` has the correct value.  

```
Seed:86
PTLRH
PatchNum:607
Time:2 min. 22 sec.
CompileError:562
TestFailure:44
```
```
---	math/math30/buggy/org/apache/commons/math3/stat/inference/MannWhitneyUTest.java
+++	math/math30/confix/org/apache/commons/math3/stat/inference/MannWhitneyUTest.java
@@ -174,7 +174,7 @@
 
         // http://en.wikipedia.org/wiki/Mann%E2%80%93Whitney_U#Normal_approximation
         final double EU = n1n2prod / 2.0;
-        final double VarU = n1n2prod * (n1 + n2 + 1) / 12.0;
+        final double VarU = n1n2prod * (((double) n1) + n2 + 1) / 12.0;
 
         final double z = (Umin - EU) / FastMath.sqrt(VarU);
 
```
## math40

### Human

```
---	math/math40/buggy/org/apache/commons/math/analysis/solvers/BracketingNthOrderBrentSolver.java
+++	math/math40/human/org/apache/commons/math/analysis/solvers/BracketingNthOrderBrentSolver.java
@@ -232,10 +232,16 @@
             double targetY;
             if (agingA >= MAXIMAL_AGING) {
                 // we keep updating the high bracket, try to compensate this
-                targetY = -REDUCTION_FACTOR * yB;
+                final int p = agingA - MAXIMAL_AGING;
+                final double weightA = (1 << p) - 1;
+                final double weightB = p + 1;
+                targetY = (weightA * yA - weightB * REDUCTION_FACTOR * yB) / (weightA + weightB);
             } else if (agingB >= MAXIMAL_AGING) {
                 // we keep updating the low bracket, try to compensate this
-                targetY = -REDUCTION_FACTOR * yA;
+                final int p = agingB - MAXIMAL_AGING;
+                final double weightA = p + 1;
+                final double weightB = (1 << p) - 1;
+                targetY = (weightB * yB - weightA * REDUCTION_FACTOR * yA) / (weightA + weightB);
             } else {
                 // bracketing is balanced, try to find the root itself
                 targetY = 0;
```
### ConFix

**Plausible**

```
Seed:20
PTLRH
PatchNum:609
Time:2 min. 53 sec.
CompileError:530
TestFailure:78
```
```
---	math/math40/buggy/org/apache/commons/math/analysis/solvers/BracketingNthOrderBrentSolver.java
+++	math/math40/confix/org/apache/commons/math/analysis/solvers/BracketingNthOrderBrentSolver.java
@@ -257,7 +257,7 @@
                     // the guessed root is either not strictly inside the interval or it
                     // is a NaN (which occurs when some sampling points share the same y)
                     // we try again with a lower interpolation order
-                    if (signChangeIndex - start >= end - signChangeIndex) {
+                    if (agingA + 1 - start >= end - signChangeIndex) {
                         // we have more points before the sign change, drop the lowest point
                         ++start;
                     } else {
```
## math41

### Human

```
---	math/math41/buggy/org/apache/commons/math/stat/descriptive/moment/Variance.java
+++	math/math41/human/org/apache/commons/math/stat/descriptive/moment/Variance.java
@@ -517,7 +517,7 @@
                 }
 
                 double sumWts = 0;
-                for (int i = 0; i < weights.length; i++) {
+                for (int i = begin; i < begin + length; i++) {
                     sumWts += weights[i];
                 }
 
```
### ConFix

**Plausible**

```
Seed:44
PLRT
PatchNum:2156
Time:3 min. 25 sec.
CompileError:1871
TestFailure:283
```
```
---	math/math41/buggy/org/apache/commons/math/stat/descriptive/moment/Variance.java
+++	math/math41/confix/org/apache/commons/math/stat/descriptive/moment/Variance.java
@@ -317,7 +317,7 @@
 
         if (test(values, weights,begin, length)) {
             clear();
-            if (length == 1) {
+            if ((+9 <= length && begin > 0) || (length < +9 && begin >= 0)) {
                 var = 0.0;
             } else if (length > 1) {
                 Mean mean = new Mean();
```
## math42

### Human

```
---	math/math42/buggy/org/apache/commons/math/optimization/linear/SimplexTableau.java
+++	math/math42/human/org/apache/commons/math/optimization/linear/SimplexTableau.java
@@ -407,10 +407,12 @@
             continue;
           }
           Integer basicRow = getBasicRow(colIndex);
+          if (basicRow != null && basicRow == 0) {
               // if the basic row is found to be the objective function row
               // set the coefficient to 0 -> this case handles unconstrained 
               // variables that are still part of the objective function
-          if (basicRows.contains(basicRow)) {
+              coefficients[i] = 0;
+          } else if (basicRows.contains(basicRow)) {
               // if multiple variables can take a given value
               // then we choose the first and set the rest equal to 0
               coefficients[i] = 0 - (restrictToNonNegative ? 0 : mostNegative);
```
### ConFix

**Plausible**

```
Seed:49
PTLRH
PatchNum:3540
Time:6 min. 57 sec.
CompileError:3151
TestFailure:388
```
```
---	math/math42/buggy/org/apache/commons/math/optimization/linear/SimplexTableau.java
+++	math/math42/confix/org/apache/commons/math/optimization/linear/SimplexTableau.java
@@ -192,7 +192,7 @@
         RealVector objectiveCoefficients =
             maximize ? f.getCoefficients().mapMultiply(-1) : f.getCoefficients();
         copyArray(objectiveCoefficients.toArray(), matrix.getDataRef()[zIndex]);
-        matrix.setEntry(zIndex, width - 1,
+        matrix.setEntry(zIndex, height - 1,
             maximize ? f.getConstantTerm() : -1 * f.getConstantTerm());
 
         if (!restrictToNonNegative) {
```
## math49

### Human

```
---	math/math49/buggy/org/apache/commons/math/linear/OpenMapRealVector.java
+++	math/math49/human/org/apache/commons/math/linear/OpenMapRealVector.java
@@ -342,7 +342,7 @@
     public OpenMapRealVector ebeDivide(RealVector v) {
         checkVectorDimensions(v.getDimension());
         OpenMapRealVector res = new OpenMapRealVector(this);
-        Iterator iter = res.entries.iterator();
+        Iterator iter = entries.iterator();
         while (iter.hasNext()) {
             iter.advance();
             res.setEntry(iter.key(), iter.value() / v.getEntry(iter.key()));
@@ -355,7 +355,7 @@
     public OpenMapRealVector ebeDivide(double[] v) {
         checkVectorDimensions(v.length);
         OpenMapRealVector res = new OpenMapRealVector(this);
-        Iterator iter = res.entries.iterator();
+        Iterator iter = entries.iterator();
         while (iter.hasNext()) {
             iter.advance();
             res.setEntry(iter.key(), iter.value() / v[iter.key()]);
@@ -367,7 +367,7 @@
     public OpenMapRealVector ebeMultiply(RealVector v) {
         checkVectorDimensions(v.getDimension());
         OpenMapRealVector res = new OpenMapRealVector(this);
-        Iterator iter = res.entries.iterator();
+        Iterator iter = entries.iterator();
         while (iter.hasNext()) {
             iter.advance();
             res.setEntry(iter.key(), iter.value() * v.getEntry(iter.key()));
@@ -380,7 +380,7 @@
     public OpenMapRealVector ebeMultiply(double[] v) {
         checkVectorDimensions(v.length);
         OpenMapRealVector res = new OpenMapRealVector(this);
-        Iterator iter = res.entries.iterator();
+        Iterator iter = entries.iterator();
         while (iter.hasNext()) {
             iter.advance();
             res.setEntry(iter.key(), iter.value() * v[iter.key()]);
```
### ConFix

**Plausible**

```
Seed:26
PTLRH
PatchNum:348
Time:1 min. 44 sec.
CompileError:330
TestFailure:17
```
```
---	math/math49/buggy/org/apache/commons/math/linear/OpenMapRealVector.java
+++	math/math49/confix/org/apache/commons/math/linear/OpenMapRealVector.java
@@ -661,7 +661,7 @@
     /** {@inheritDoc} */
     public void setEntry(int index, double value) {
         checkIndex(index);
-        if (!isDefaultValue(value)) {
+        if (!isDefaultValue(epsilon)) {
             entries.put(index, value);
         } else if (entries.containsKey(index)) {
             entries.remove(index);
```
## math5

### Human

```
---	math/math5/buggy/org/apache/commons/math3/complex/Complex.java
+++	math/math5/human/org/apache/commons/math3/complex/Complex.java
@@ -302,7 +302,7 @@
         }
 
         if (real == 0.0 && imaginary == 0.0) {
-            return NaN;
+            return INF;
         }
 
         if (isInfinite) {
```
### ConFix

**Identical**  
ConFix patch is identical to Human patch.  

```
Seed:54
PTLRH
PatchNum:198
Time:4 min. 36 sec.
CompileError:14
TestFailure:183
```
```
---	math/math5/buggy/org/apache/commons/math3/complex/Complex.java
+++	math/math5/confix/org/apache/commons/math3/complex/Complex.java
@@ -302,7 +302,7 @@
         }
 
         if (real == 0.0 && imaginary == 0.0) {
-            return NaN;
+            return INF;
         }
 
         if (isInfinite) {
```
## math50

### Human

```
---	math/math50/buggy/org/apache/commons/math/analysis/solvers/BaseSecantSolver.java
+++	math/math50/human/org/apache/commons/math/analysis/solvers/BaseSecantSolver.java
@@ -184,10 +184,6 @@
                     break;
                 case REGULA_FALSI:
                     // Nothing.
-                    if (x == x1) {
-                        x0 = 0.5 * (x0 + x1 - FastMath.max(rtol * FastMath.abs(x1), atol));
-                        f0 = computeObjectiveValue(x0);
-                    }
                     break;
                 default:
                     // Should never happen.
```
### ConFix

**Plausible**

```
Seed:91
PTLRH
PatchNum:155
Time:1 min. 33 sec.
CompileError:139
TestFailure:15
```
```
---	math/math50/buggy/org/apache/commons/math/analysis/solvers/BaseSecantSolver.java
+++	math/math50/confix/org/apache/commons/math/analysis/solvers/BaseSecantSolver.java
@@ -185,7 +185,7 @@
                 case REGULA_FALSI:
                     // Nothing.
                     if (x == x1) {
-                        x0 = 0.5 * (x0 + x1 - FastMath.max(rtol * FastMath.abs(x1), atol));
+                        x0 = 0.5 * (x0 + x0 - FastMath.max(rtol * FastMath.abs(x1), atol));
                         f0 = computeObjectiveValue(x0);
                     }
                     break;
```
## math57

### Human

```
---	math/math57/buggy/org/apache/commons/math/stat/clustering/KMeansPlusPlusClusterer.java
+++	math/math57/human/org/apache/commons/math/stat/clustering/KMeansPlusPlusClusterer.java
@@ -172,7 +172,7 @@
         while (resultSet.size() < k) {
             // For each data point x, compute D(x), the distance between x and
             // the nearest center that has already been chosen.
-            int sum = 0;
+            double sum = 0;
             for (int i = 0; i < pointSet.size(); i++) {
                 final T p = pointSet.get(i);
                 final Cluster<T> nearest = getNearestCluster(resultSet, p);
```
### ConFix

**Plausible**

```
Seed:42
PTLRH
PatchNum:1098
Time:3 min. 26 sec.
CompileError:1039
TestFailure:58
```
```
---	math/math57/buggy/org/apache/commons/math/stat/clustering/KMeansPlusPlusClusterer.java
+++	math/math57/confix/org/apache/commons/math/stat/clustering/KMeansPlusPlusClusterer.java
@@ -96,7 +96,7 @@
         assignPointsToClusters(clusters, points);
 
         // iterate through updating the centers until we're done
-        final int max = (maxIterations < 0) ? Integer.MAX_VALUE : maxIterations;
+        final int max = (maxIterations < +1) ? Integer.MAX_VALUE : maxIterations;
         for (int count = 0; count < max; count++) {
             boolean clusteringChanged = false;
             List<Cluster<T>> newClusters = new ArrayList<Cluster<T>>();
```
## math62

### Human

```
---	math/math62/buggy/org/apache/commons/math/optimization/univariate/MultiStartUnivariateRealOptimizer.java
+++	math/math62/human/org/apache/commons/math/optimization/univariate/MultiStartUnivariateRealOptimizer.java
@@ -143,7 +143,7 @@
                                                  final GoalType goal,
                                                  final double min, final double max)
         throws FunctionEvaluationException {
-        return optimize(f, goal, min, max, 0);
+        return optimize(f, goal, min, max, min + 0.5 * (max - min));
     }
 
     /** {@inheritDoc} */
@@ -157,9 +157,8 @@
         // Multi-start loop.
         for (int i = 0; i < starts; ++i) {
             try {
-                final double bound1 = (i == 0) ? min : min + generator.nextDouble() * (max - min);
-                final double bound2 = (i == 0) ? max : min + generator.nextDouble() * (max - min);
-                optima[i] = optimizer.optimize(f, goal, FastMath.min(bound1, bound2), FastMath.max(bound1, bound2));
+                final double s = (i == 0) ? startValue : min + generator.nextDouble() * (max - min);
+                optima[i] = optimizer.optimize(f, goal, min, max, s);
             } catch (FunctionEvaluationException fee) {
                 optima[i] = null;
             } catch (ConvergenceException ce) {
```
### ConFix

**Plausible**

```
Seed:96
PTLRH
PatchNum:224
Time:1 min. 16 sec.
CompileError:215
TestFailure:8
```
```
---	math/math62/buggy/org/apache/commons/math/optimization/univariate/MultiStartUnivariateRealOptimizer.java
+++	math/math62/confix/org/apache/commons/math/optimization/univariate/MultiStartUnivariateRealOptimizer.java
@@ -159,7 +159,7 @@
             try {
                 final double bound1 = (i == 0) ? min : min + generator.nextDouble() * (max - min);
                 final double bound2 = (i == 0) ? max : min + generator.nextDouble() * (max - min);
-                optima[i] = optimizer.optimize(f, goal, FastMath.min(bound1, bound2), FastMath.max(bound1, bound2));
+                optima[i] = optimizer.optimize(f, goal, FastMath.min(min, bound2), FastMath.max(bound1, bound2));
             } catch (FunctionEvaluationException fee) {
                 optima[i] = null;
             } catch (ConvergenceException ce) {
```
## math63

### Human

```
---	math/math63/buggy/org/apache/commons/math/util/MathUtils.java
+++	math/math63/human/org/apache/commons/math/util/MathUtils.java
@@ -414,7 +414,7 @@
      * @return {@code true} if the values are equal.
      */
     public static boolean equals(double x, double y) {
-        return (Double.isNaN(x) && Double.isNaN(y)) || x == y;
+        return equals(x, y, 1);
     }
 
     /**
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the same return statement as Human patch.  

```
Seed:13
PTLRH
PatchNum:82
Time:1 min. 41 sec.
CompileError:50
TestFailure:31
```
```
---	math/math63/buggy/org/apache/commons/math/util/MathUtils.java
+++	math/math63/confix/org/apache/commons/math/util/MathUtils.java
@@ -414,7 +414,7 @@
      * @return {@code true} if the values are equal.
      */
     public static boolean equals(double x, double y) {
-        return (Double.isNaN(x) && Double.isNaN(y)) || x == y;
+        return (Double.isNaN(SAFE_MIN) && Double.isNaN(y)) || x == y;
     }
 
     /**
```
## math64

### Human

```
---	math/math64/buggy/org/apache/commons/math/optimization/general/LevenbergMarquardtOptimizer.java
+++	math/math64/human/org/apache/commons/math/optimization/general/LevenbergMarquardtOptimizer.java
@@ -255,6 +255,8 @@
         double[] diag    = new double[cols];
         double[] oldX    = new double[cols];
         double[] oldRes  = new double[rows];
+        double[] oldObj  = new double[rows];
+        double[] qtf     = new double[rows];
         double[] work1   = new double[cols];
         double[] work2   = new double[cols];
         double[] work3   = new double[cols];
@@ -267,6 +269,9 @@
         boolean firstIteration = true;
         VectorialPointValuePair current = new VectorialPointValuePair(point, objective);
         while (true) {
+            for (int i=0;i<rows;i++) {
+                qtf[i]=residuals[i];
+            }
             incrementIterationsCounter();
 
             // compute the Q.R. decomposition of the jacobian matrix
@@ -275,7 +280,7 @@
             qrDecomposition();
 
             // compute Qt.res
-            qTy(residuals);
+            qTy(qtf);
             // now we don't need Q anymore,
             // so let jacobian contain the R matrix with its diagonal elements
             for (int k = 0; k < solvedCols; ++k) {
@@ -313,7 +318,7 @@
                     if (s != 0) {
                         double sum = 0;
                         for (int i = 0; i <= j; ++i) {
-                            sum += jacobian[i][pj] * residuals[i];
+                            sum += jacobian[i][pj] * qtf[i];
                         }
                         maxCosine = Math.max(maxCosine, Math.abs(sum) / (s * cost));
                     }
@@ -321,6 +326,8 @@
             }
             if (maxCosine <= orthoTolerance) {
                 // convergence has been reached
+            	updateResidualsAndCost();
+            	current = new VectorialPointValuePair(point, objective);
                 return current;
             }
 
@@ -341,9 +348,12 @@
                 double[] tmpVec = residuals;
                 residuals = oldRes;
                 oldRes    = tmpVec;
+                tmpVec    = objective;
+                objective = oldObj;
+                oldObj    = tmpVec;
 
                 // determine the Levenberg-Marquardt parameter
-                determineLMParameter(oldRes, delta, diag, work1, work2, work3);
+                determineLMParameter(qtf, delta, diag, work1, work2, work3);
 
                 // compute the new point and the norm of the evolution direction
                 double lmNorm = 0;
@@ -362,7 +372,6 @@
 
                 // evaluate the function at x + p and calculate its norm
                 updateResidualsAndCost();
-                current = new VectorialPointValuePair(point, objective);
 
                 // compute the scaled actual reduction
                 double actRed = -1.0;
@@ -418,9 +427,15 @@
                         xNorm    += xK * xK;
                     }
                     xNorm = Math.sqrt(xNorm);
+                    current = new VectorialPointValuePair(point, objective);
 
                     // tests for convergence.
+                    if (checker != null) {
                     // we use the vectorial convergence checker
+                    	if (checker.converged(getIterations(), previous, current)) {
+                    		return current;
+                    	}
+                    }
                 } else {
                     // failed iteration, reset the previous values
                     cost = previousCost;
@@ -431,6 +446,9 @@
                     tmpVec    = residuals;
                     residuals = oldRes;
                     oldRes    = tmpVec;
+                    tmpVec    = objective;
+                    objective = oldObj;
+                    oldObj    = tmpVec;
                 }
                 if (checker==null) {
                 	if (((Math.abs(actRed) <= costRelativeTolerance) &&
@@ -439,10 +457,6 @@
                        (delta <= parRelativeTolerance * xNorm)) {
                        return current;
                    }
-                } else {
-                    if (checker.converged(getIterations(), previous, current)) {
-                        return current;
-                    }
                 }
                 // tests for termination and stringent tolerances
                 // (2.2204e-16 is the machine epsilon for IEEE754)
```
### ConFix

**Plausible**

```
Seed:46
PTLRH
PatchNum:8956
Time:13 min. 39 sec.
CompileError:7273
TestFailure:1682
```
```
---	math/math64/buggy/org/apache/commons/math/optimization/general/LevenbergMarquardtOptimizer.java
+++	math/math64/confix/org/apache/commons/math/optimization/general/LevenbergMarquardtOptimizer.java
@@ -362,7 +362,7 @@
 
                 // evaluate the function at x + p and calculate its norm
                 updateResidualsAndCost();
-                current = new VectorialPointValuePair(point, objective);
+                current = new VectorialPointValuePair(point, objective, false);
 
                 // compute the scaled actual reduction
                 double actRed = -1.0;
```
## math7

### Human

```
---	math/math7/buggy/org/apache/commons/math3/ode/AbstractIntegrator.java
+++	math/math7/human/org/apache/commons/math3/ode/AbstractIntegrator.java
@@ -343,8 +343,10 @@
                 final double[] eventY = interpolator.getInterpolatedState().clone();
 
                 // advance all event states to current time
-                currentEvent.stepAccepted(eventT, eventY);
-                isLastStep = currentEvent.stop();
+                for (final EventState state : eventsStates) {
+                    state.stepAccepted(eventT, eventY);
+                    isLastStep = isLastStep || state.stop();
+                }
 
                 // handle the first part of the step, up to the event
                 for (final StepHandler handler : stepHandlers) {
@@ -354,22 +356,19 @@
                 if (isLastStep) {
                     // the event asked to stop integration
                     System.arraycopy(eventY, 0, y, 0, y.length);
-                    for (final EventState remaining : occuringEvents) {
-                        remaining.stepAccepted(eventT, eventY);
-                    }
                     return eventT;
                 }
 
-                boolean needReset = currentEvent.reset(eventT, eventY);
+                boolean needReset = false;
+                for (final EventState state : eventsStates) {
+                    needReset =  needReset || state.reset(eventT, eventY);
+                }
                 if (needReset) {
                     // some event handler has triggered changes that
                     // invalidate the derivatives, we need to recompute them
                     System.arraycopy(eventY, 0, y, 0, y.length);
                     computeDerivatives(eventT, y, yDot);
                     resetOccurred = true;
-                    for (final EventState remaining : occuringEvents) {
-                        remaining.stepAccepted(eventT, eventY);
-                    }
                     return eventT;
                 }
 
```
### ConFix

**Plausible**

```
Seed:51
PTLRH
PatchNum:2864
Time:8 min. 54 sec.
CompileError:2709
TestFailure:154
```
```
---	math/math7/buggy/org/apache/commons/math3/ode/AbstractIntegrator.java
+++	math/math7/confix/org/apache/commons/math3/ode/AbstractIntegrator.java
@@ -106,7 +106,8 @@
 
     /** {@inheritDoc} */
     public void addStepHandler(final StepHandler handler) {
-        stepHandlers.add(handler);
+        clearEventHandlers();
+		stepHandlers.add(handler);
     }
 
     /** {@inheritDoc} */
```
## math75

### Human

```
---	math/math75/buggy/org/apache/commons/math/stat/Frequency.java
+++	math/math75/human/org/apache/commons/math/stat/Frequency.java
@@ -300,7 +300,7 @@
      */
     @Deprecated
     public double getPct(Object v) {
-        return getCumPct((Comparable<?>) v);
+        return getPct((Comparable<?>) v);
     }
 
     /**
```
### ConFix

**Identical**  
ConFix patch is identical to Human patch.  

```
Seed:45
PLRT
PatchNum:2198
Time:2 min. 37 sec.
CompileError:2008
TestFailure:188
```
```
---	math/math75/buggy/org/apache/commons/math/stat/Frequency.java
+++	math/math75/confix/org/apache/commons/math/stat/Frequency.java
@@ -300,7 +300,7 @@
      */
     @Deprecated
     public double getPct(Object v) {
-        return getCumPct((Comparable<?>) v);
+        return getPct((Comparable<?>) v);
     }
 
     /**
```
## math78

### Human

```
---	math/math78/buggy/org/apache/commons/math/ode/events/EventState.java
+++	math/math78/human/org/apache/commons/math/ode/events/EventState.java
@@ -188,6 +188,7 @@
                 if (g0Positive ^ (gb >= 0)) {
                     // there is a sign change: an event is expected during this step
 
+                    if (ga * gb > 0) {
                         // this is a corner case:
                         // - there was an event near ta,
                         // - there is another event between ta and tb
@@ -195,7 +196,17 @@
                         // this implies that the real sign of ga is the same as gb, so we need to slightly
                         // shift ta to make sure ga and gb get opposite signs and the solver won't complain
                         // about bracketing
+                        final double epsilon = (forward ? 0.25 : -0.25) * convergence;
+                        for (int k = 0; (k < 4) && (ga * gb > 0); ++k) {
+                            ta += epsilon;
+                            interpolator.setInterpolatedTime(ta);
+                            ga = handler.g(ta, interpolator.getInterpolatedState());
+                        }
+                        if (ga * gb > 0) {
                             // this should never happen
+                            throw MathRuntimeException.createInternalError(null);
+                        }
+                    }
                          
                     // variation direction, with respect to the integration direction
                     increasing = gb >= ga;
```
### ConFix

**Plausible**

```
Seed:49
PTLRH
PatchNum:296
Time:29 min. 21 sec.
CompileError:242
TestFailure:53
```
```
---	math/math78/buggy/org/apache/commons/math/ode/events/EventState.java
+++	math/math78/confix/org/apache/commons/math/ode/events/EventState.java
@@ -24,6 +24,7 @@
 import org.apache.commons.math.analysis.solvers.BrentSolver;
 import org.apache.commons.math.ode.DerivativeException;
 import org.apache.commons.math.ode.sampling.StepInterpolator;
+import java.util.Map;
 
 /** This class handles the state for one {@link EventHandler
  * event handler} during integration steps.
@@ -94,7 +95,7 @@
     public EventState(final EventHandler handler, final double maxCheckInterval,
                       final double convergence, final int maxIterationCount) {
         this.handler           = handler;
-        this.maxCheckInterval  = maxCheckInterval;
+        this.maxCheckInterval  = t0;
         this.convergence       = Math.abs(convergence);
         this.maxIterationCount = maxIterationCount;
 
```
## math79

### Human

```
---	math/math79/buggy/org/apache/commons/math/util/MathUtils.java
+++	math/math79/human/org/apache/commons/math/util/MathUtils.java
@@ -1621,9 +1621,9 @@
      * @return the L<sub>2</sub> distance between the two points
      */
     public static double distance(int[] p1, int[] p2) {
-      int sum = 0;
+      double sum = 0;
       for (int i = 0; i < p1.length; i++) {
-          final int dp = p1[i] - p2[i];
+          final double dp = p1[i] - p2[i];
           sum += dp * dp;
       }
       return Math.sqrt(sum);
```
### ConFix

**Plausible**  
**Informative - Partial**  
ConFix patch addresses the overflow issue for `dp * dp`, but it doesn't address overflow issue for `sum` unlike Human patch.  

```
Seed:99
PLRT
PatchNum:3834
Time:9 min. 59 sec.
CompileError:1617
TestFailure:2215
```
```
---	math/math79/buggy/org/apache/commons/math/util/MathUtils.java
+++	math/math79/confix/org/apache/commons/math/util/MathUtils.java
@@ -1624,7 +1624,7 @@
       int sum = 0;
       for (int i = 0; i < p1.length; i++) {
           final int dp = p1[i] - p2[i];
-          sum += dp * dp;
+          sum += ((double) dp) * dp;
       }
       return Math.sqrt(sum);
     }
```
## math8

### Human

```
---	math/math8/buggy/org/apache/commons/math3/distribution/DiscreteDistribution.java
+++	math/math8/human/org/apache/commons/math3/distribution/DiscreteDistribution.java
@@ -178,13 +178,13 @@
      * @throws NotStrictlyPositiveException if {@code sampleSize} is not
      * positive.
      */
-    public T[] sample(int sampleSize) throws NotStrictlyPositiveException {
+    public Object[] sample(int sampleSize) throws NotStrictlyPositiveException {
         if (sampleSize <= 0) {
             throw new NotStrictlyPositiveException(LocalizedFormats.NUMBER_OF_SAMPLES,
                     sampleSize);
         }
 
-        final T[]out = (T[]) java.lang.reflect.Array.newInstance(singletons.get(0).getClass(), sampleSize);
+        final Object[] out = new Object[sampleSize];
 
         for (int i = 0; i < sampleSize; i++) {
             out[i] = sample();
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the same variable declaration as Human patch.  
We didn't count the method declaration part, since it is only dependent to the variable declaration modification.  

```
Seed:97
PTLRH
PatchNum:20
Time:3 min. 35 sec.
CompileError:17
TestFailure:2
```
```
---	math/math8/buggy/org/apache/commons/math3/distribution/DiscreteDistribution.java
+++	math/math8/confix/org/apache/commons/math3/distribution/DiscreteDistribution.java
@@ -184,7 +184,7 @@
                     sampleSize);
         }
 
-        final T[]out = (T[]) java.lang.reflect.Array.newInstance(singletons.get(0).getClass(), sampleSize);
+        final T[]out = (T[]) java.lang.reflect.Array.newInstance(singletons.get(sampleSize).getClass(), sampleSize);
 
         for (int i = 0; i < sampleSize; i++) {
             out[i] = sample();
```
## math80

### Human

```
---	math/math80/buggy/org/apache/commons/math/linear/EigenDecompositionImpl.java
+++	math/math80/human/org/apache/commons/math/linear/EigenDecompositionImpl.java
@@ -1132,7 +1132,7 @@
     private boolean flipIfWarranted(final int n, final int step) {
         if (1.5 * work[pingPong] < work[4 * (n - 1) + pingPong]) {
             // flip array
-            int j = 4 * n - 1;
+            int j = 4 * (n - 1);
             for (int i = 0; i < j; i += 4) {
                 for (int k = 0; k < 4; k += step) {
                     final double tmp = work[i + k];
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the value of `j` like Human patch.  

```
Seed:79
PTLRH
PatchNum:82
Time:4 min. 2 sec.
CompileError:24
TestFailure:57
```
```
---	math/math80/buggy/org/apache/commons/math/linear/EigenDecompositionImpl.java
+++	math/math80/confix/org/apache/commons/math/linear/EigenDecompositionImpl.java
@@ -1132,7 +1132,7 @@
     private boolean flipIfWarranted(final int n, final int step) {
         if (1.5 * work[pingPong] < work[4 * (n - 1) + pingPong]) {
             // flip array
-            int j = 4 * n - 1;
+            int j = 4 * tType - 1;
             for (int i = 0; i < j; i += 4) {
                 for (int k = 0; k < 4; k += step) {
                     final double tmp = work[i + k];
```
## math81

### Human

```
---	math/math81/buggy/org/apache/commons/math/linear/EigenDecompositionImpl.java
+++	math/math81/human/org/apache/commons/math/linear/EigenDecompositionImpl.java
@@ -600,6 +600,7 @@
         lowerSpectra = Math.min(lowerSpectra, lower);
         final double upper = dCurrent + eCurrent;
         work[upperStart + m - 1] = upper;
+        upperSpectra = Math.max(upperSpectra, upper);
         minPivot = MathUtils.SAFE_MIN * Math.max(1.0, eMax * eMax);
 
     }
@@ -902,8 +903,8 @@
                     diagMax    = work[4 * i0];
                     offDiagMin = work[4 * i0 + 2];
                     double previousEMin = work[4 * i0 + 3];
-                    for (int i = 4 * i0; i < 4 * n0 - 11; i += 4) {
-                        if ((work[i + 3] <= TOLERANCE_2 * work[i]) &&
+                    for (int i = 4 * i0; i < 4 * n0 - 16; i += 4) {
+                        if ((work[i + 3] <= TOLERANCE_2 * work[i]) ||
                             (work[i + 2] <= TOLERANCE_2 * sigma)) {
                             // insert a split
                             work[i + 2]  = -sigma;
@@ -1540,7 +1541,7 @@
                 double a2 = (work[np - 8] / b2) * (1 + work[np - 4] / b1);
 
                 // approximate contribution to norm squared from i < nn-2.
-                if (end - start > 2) {
+                if (end - start > 3) {
                     b2 = work[nn - 13] / work[nn - 15];
                     a2 = a2 + b2;
                     for (int i4 = nn - 17; i4 >= 4 * start + 2 + pingPong; i4 -= 4) {
```
### ConFix

**Plausible**

```
Seed:58
PTLRH
PatchNum:1
Time:48 sec.
CompileError:0
TestFailure:0
```
```
---	math/math81/buggy/org/apache/commons/math/linear/EigenDecompositionImpl.java
+++	math/math81/confix/org/apache/commons/math/linear/EigenDecompositionImpl.java
@@ -1523,7 +1523,7 @@
                     tau = s;
 
                 }
-            } else if (dMin == dN2) {
+            } else if (sigmaLow == dN2) {
 
                 // case 5.
                 tType = -5;
```
## math82

### Human

```
---	math/math82/buggy/org/apache/commons/math/optimization/linear/SimplexSolver.java
+++	math/math82/human/org/apache/commons/math/optimization/linear/SimplexSolver.java
@@ -79,7 +79,7 @@
         for (int i = tableau.getNumObjectiveFunctions(); i < tableau.getHeight(); i++) {
             final double rhs = tableau.getEntry(i, tableau.getWidth() - 1);
             final double entry = tableau.getEntry(i, col);
-            if (MathUtils.compareTo(entry, 0, epsilon) >= 0) {
+            if (MathUtils.compareTo(entry, 0, epsilon) > 0) {
                 final double ratio = rhs / entry;
                 if (ratio < minRatio) {
                     minRatio = ratio;
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the same if condition as Human patch.  

```
Seed:91
PTLRH
PatchNum:87
Time:47 sec.
CompileError:83
TestFailure:3
```
```
---	math/math82/buggy/org/apache/commons/math/optimization/linear/SimplexSolver.java
+++	math/math82/confix/org/apache/commons/math/optimization/linear/SimplexSolver.java
@@ -79,7 +79,7 @@
         for (int i = tableau.getNumObjectiveFunctions(); i < tableau.getHeight(); i++) {
             final double rhs = tableau.getEntry(i, tableau.getWidth() - 1);
             final double entry = tableau.getEntry(i, col);
-            if (MathUtils.compareTo(entry, 0, epsilon) >= 0) {
+            if (MathUtils.compareTo(entry, 0, entry) >= 0) {
                 final double ratio = rhs / entry;
                 if (ratio < minRatio) {
                     minRatio = ratio;
```
## math84

### Human

```
---	math/math84/buggy/org/apache/commons/math/optimization/direct/MultiDirectional.java
+++	math/math84/human/org/apache/commons/math/optimization/direct/MultiDirectional.java
@@ -61,6 +61,7 @@
     protected void iterateSimplex(final Comparator<RealPointValuePair> comparator)
         throws FunctionEvaluationException, OptimizationException, IllegalArgumentException {
 
+        final RealConvergenceChecker checker = getConvergenceChecker();
         while (true) {
 
             incrementIterationsCounter();
@@ -89,8 +90,16 @@
             final RealPointValuePair contracted = evaluateNewSimplex(original, gamma, comparator);
             if (comparator.compare(contracted, best) < 0) {
                 // accept the contracted simplex
+                return;
+            }
 
             // check convergence
+            final int iter = getIterations();
+            boolean converged = true;
+            for (int i = 0; i < simplex.length; ++i) {
+                converged &= checker.converged(iter, original[i], simplex[i]);
+            }
+            if (converged) {
                 return;
             }
 
```
### ConFix

**Plausible**

```
Seed:77
PTLRH
PatchNum:1145
Time:1 min. 31 sec.
CompileError:1048
TestFailure:96
```
```
---	math/math84/buggy/org/apache/commons/math/optimization/direct/MultiDirectional.java
+++	math/math84/confix/org/apache/commons/math/optimization/direct/MultiDirectional.java
@@ -87,7 +87,7 @@
 
             // compute the contracted simplex
             final RealPointValuePair contracted = evaluateNewSimplex(original, gamma, comparator);
-            if (comparator.compare(contracted, best) < 0) {
+            if (comparator.compare(contracted, best) < 127) {
                 // accept the contracted simplex
 
             // check convergence
```
## math85

### Human

```
---	math/math85/buggy/org/apache/commons/math/analysis/solvers/UnivariateRealSolverUtils.java
+++	math/math85/human/org/apache/commons/math/analysis/solvers/UnivariateRealSolverUtils.java
@@ -195,7 +195,7 @@
         } while ((fa * fb > 0.0) && (numIterations < maximumIterations) && 
                 ((a > lowerBound) || (b < upperBound)));
    
-        if (fa * fb >= 0.0 ) {
+        if (fa * fb > 0.0 ) {
             throw new ConvergenceException(
                       "number of iterations={0}, maximum iterations={1}, " +
                       "initial={2}, lower bound={3}, upper bound={4}, final a value={5}, " +
```
### ConFix

**Plausible**  
**Informative - SameLoc**  
ConFix patch modifies the same if condition as Human patch.  

```
Seed:1
PLRT
PatchNum:612
Time:3 min. 49 sec.
CompileError:539
TestFailure:71
```
```
---	math/math85/buggy/org/apache/commons/math/analysis/solvers/UnivariateRealSolverUtils.java
+++	math/math85/confix/org/apache/commons/math/analysis/solvers/UnivariateRealSolverUtils.java
@@ -195,7 +195,7 @@
         } while ((fa * fb > 0.0) && (numIterations < maximumIterations) && 
                 ((a > lowerBound) || (b < upperBound)));
    
-        if (fa * fb >= 0.0 ) {
+        if (fa * upperBound >= 0.0 ) {
             throw new ConvergenceException(
                       "number of iterations={0}, maximum iterations={1}, " +
                       "initial={2}, lower bound={3}, upper bound={4}, final a value={5}, " +
```
## math88

### Human

```
---	math/math88/buggy/org/apache/commons/math/optimization/linear/SimplexTableau.java
+++	math/math88/human/org/apache/commons/math/optimization/linear/SimplexTableau.java
@@ -326,19 +326,18 @@
         Integer basicRow =
             getBasicRow(getNumObjectiveFunctions() + getOriginalNumDecisionVariables());
         double mostNegative = basicRow == null ? 0 : getEntry(basicRow, getRhsOffset());
+        Set<Integer> basicRows = new HashSet<Integer>();
         for (int i = 0; i < coefficients.length; i++) {
             basicRow = getBasicRow(getNumObjectiveFunctions() + i);
+            if (basicRows.contains(basicRow)) {
                 // if multiple variables can take a given value 
                 // then we choose the first and set the rest equal to 0
+                coefficients[i] = 0;
+            } else {
+                basicRows.add(basicRow);
                 coefficients[i] =
                     (basicRow == null ? 0 : getEntry(basicRow, getRhsOffset())) -
                     (restrictToNonNegative ? 0 : mostNegative);
-            if (basicRow != null) {
-                for (int j = getNumObjectiveFunctions(); j < getNumObjectiveFunctions() + i; j++) {
-                    if (tableau.getEntry(basicRow, j) == 1) {
-                         coefficients[i] = 0;
-                    }
-                }
             }
         }
         return new RealPointValuePair(coefficients, f.getValue(coefficients));
```
### ConFix

**Plausible**

```
Seed:76
PLRT
PatchNum:9643
Time:11 min. 42 sec.
CompileError:8508
TestFailure:1133
```
```
---	math/math88/buggy/org/apache/commons/math/optimization/linear/SimplexTableau.java
+++	math/math88/confix/org/apache/commons/math/optimization/linear/SimplexTableau.java
@@ -335,7 +335,7 @@
                     (restrictToNonNegative ? 0 : mostNegative);
             if (basicRow != null) {
                 for (int j = getNumObjectiveFunctions(); j < getNumObjectiveFunctions() + i; j++) {
-                    if (tableau.getEntry(basicRow, j) == 1) {
+                    if (tableau.getEntry(basicRow, i) == 1) {
                          coefficients[i] = 0;
                     }
                 }
```
## math94

### Human

```
---	math/math94/buggy/org/apache/commons/math/util/MathUtils.java
+++	math/math94/human/org/apache/commons/math/util/MathUtils.java
@@ -409,7 +409,7 @@
      * @since 1.1
      */
     public static int gcd(int u, int v) {
-        if (u * v == 0) {
+        if ((u == 0) || (v == 0)) {
             return (Math.abs(u) + Math.abs(v));
         }
         // keep u and v negative, as negative integers range down to
```
### ConFix

**Acceptable**  
ConFix patch prevents the same overflow issue as Human patch, which discussed in https://issues.apache.org/jira/browse/MATH-238.  

```
Seed:21
PTLRH
PatchNum:13
Time:1 min. 37 sec.
CompileError:9
TestFailure:3
```
```
---	math/math94/buggy/org/apache/commons/math/util/MathUtils.java
+++	math/math94/confix/org/apache/commons/math/util/MathUtils.java
@@ -409,7 +409,7 @@
      * @since 1.1
      */
     public static int gcd(int u, int v) {
-        if (u * v == 0) {
+        if (((double) u) * v == 0) {
             return (Math.abs(u) + Math.abs(v));
         }
         // keep u and v negative, as negative integers range down to
```
## math95

### Human

```
---	math/math95/buggy/org/apache/commons/math/distribution/FDistributionImpl.java
+++	math/math95/human/org/apache/commons/math/distribution/FDistributionImpl.java
@@ -141,10 +141,12 @@
      * @return initial domain value
      */
     protected double getInitialDomain(double p) {
-        double ret;
+        double ret = 1.0;
         double d = getDenominatorDegreesOfFreedom();
+        if (d > 2.0) {
             // use mean
             ret = d / (d - 2.0);
+        }
         return ret;
     }
     
```
### ConFix

**Plausible**

```
Seed:20
PLRT
PatchNum:245
Time:57 sec.
CompileError:200
TestFailure:43
```
```
---	math/math95/buggy/org/apache/commons/math/distribution/FDistributionImpl.java
+++	math/math95/confix/org/apache/commons/math/distribution/FDistributionImpl.java
@@ -145,7 +145,7 @@
         double d = getDenominatorDegreesOfFreedom();
             // use mean
             ret = d / (d - 2.0);
-        return ret;
+        return 0;
     }
     
     /**
```
# Time

## time11

### Human

```
---	time/time11/buggy/org/joda/time/tz/ZoneInfoCompiler.java
+++	time/time11/human/org/joda/time/tz/ZoneInfoCompiler.java
@@ -65,10 +65,11 @@
 
     static Chronology cLenientISO;
 
-    static ThreadLocal<Boolean> cVerbose = new ThreadLocal<Boolean>();
-    static {
-        cVerbose.set(Boolean.FALSE);
-    }
+    static ThreadLocal<Boolean> cVerbose = new ThreadLocal<Boolean>() {
+        protected Boolean initialValue() {
+            return Boolean.FALSE;
+        }
+    };
 
     /**
      * Gets a flag indicating that verbose logging is required.
```
### ConFix

**Plausible**

```
Seed:34
PTLRH
PatchNum:39
Time:41 sec.
CompileError:35
TestFailure:3
```
```
---	time/time11/buggy/org/joda/time/tz/ZoneInfoCompiler.java
+++	time/time11/confix/org/joda/time/tz/ZoneInfoCompiler.java
@@ -75,7 +75,9 @@
      * @return true to log verbosely
      */
     public static boolean verbose() {
-        return cVerbose.get();
+        if (cLenientISO == null)
+			return false;
+		return cVerbose.get();
     }
 
     //-----------------------------------------------------------------------
```
## time4

### Human

```
---	time/time4/buggy/org/joda/time/Partial.java
+++	time/time4/human/org/joda/time/Partial.java
@@ -461,7 +461,7 @@
             System.arraycopy(iValues, i, newValues, i + 1, newValues.length - i - 1);
             // use public constructor to ensure full validation
             // this isn't overly efficient, but is safe
-            Partial newPartial = new Partial(iChronology, newTypes, newValues);
+            Partial newPartial = new Partial(newTypes, newValues, iChronology);
             iChronology.validate(newPartial, newValues);
             return newPartial;
         }
```
### ConFix

**Plausible**  
**Informative - Regression**  
The difference between Human patch and ConFix patch is calling different constructors for `newPartial`.  
Human patch provides additional verifications for values by calling `Partial(newTypes, newValues, iChronology)` instead of `Partial(iChronology, newTypes, newValues)`, as discussed in https://github.com/JodaOrg/joda-time/issues/88.  
ConFix patch calls `Partial(newTypes, newValues)`, which internally calls `Partial(newTypes, newValues, null)`.   
It works same as Human patch if `iChronology` is `null` or default value, but it works incorrectly otherwise.  

```
Seed:74
PTLRH
PatchNum:5750
Time:5 min. 9 sec.
CompileError:5412
TestFailure:337
```
```
---	time/time4/buggy/org/joda/time/Partial.java
+++	time/time4/confix/org/joda/time/Partial.java
@@ -461,7 +461,7 @@
             System.arraycopy(iValues, i, newValues, i + 1, newValues.length - i - 1);
             // use public constructor to ensure full validation
             // this isn't overly efficient, but is safe
-            Partial newPartial = new Partial(iChronology, newTypes, newValues);
+            Partial newPartial = new Partial(newTypes, newValues);
             iChronology.validate(newPartial, newValues);
             return newPartial;
         }
```
## time7

### Human

```
---	time/time7/buggy/org/joda/time/format/DateTimeFormatter.java
+++	time/time7/human/org/joda/time/format/DateTimeFormatter.java
@@ -705,9 +705,9 @@
         
         long instantMillis = instant.getMillis();
         Chronology chrono = instant.getChronology();
+        int defaultYear = DateTimeUtils.getChronology(chrono).year().get(instantMillis);
         long instantLocal = instantMillis + chrono.getZone().getOffset(instantMillis);
         chrono = selectChronology(chrono);
-        int defaultYear = chrono.year().get(instantLocal);
         
         DateTimeParserBucket bucket = new DateTimeParserBucket(
             instantLocal, chrono, iLocale, iPivotYear, defaultYear);
```
### ConFix

**Plausible**  
**Informative - Regression**  
The key issue is that `defaultYear` value is decided by `instantLocal`, not `instantMillis`.  
Hence Human patch updates `instantLocal` to `instantMillis`.    
It is also delivered by ConFix patch.  

The key difference between Human patch and ConFix patch is the location of `defaultYear` declaration.  
In Human patch, `chrono` is relaced with `DateTimeUtils.getChronology(chrono)`.  
ConFix patch also has similar effect, since `selectChronology(chrono)` is defined as follows.  
```
    private Chronology selectChronology(Chronology chrono) {
        chrono = DateTimeUtils.getChronology(chrono);
        if (iChrono != null) {
            chrono = iChrono;
        }   
        if (iZone != null) {
            chrono = chrono.withZone(iZone);
        }   
        return chrono;
    }
```
In ConFix patch, `chrono` has the return value of `selectChronology(chrono)`, which will be one of `DateTimeUtils.getChronology(chrono)`, `iChrono`, or `chrono.withZone(iZone)`.  
Hence ConFix patch has the regression when `iChrono != null` or `iZone != null`, although it addresses the issue.  

```
Seed:90
PTLRH
PatchNum:395
Time:1 min. 13 sec.
CompileError:384
TestFailure:10
```
```
---	time/time7/buggy/org/joda/time/format/DateTimeFormatter.java
+++	time/time7/confix/org/joda/time/format/DateTimeFormatter.java
@@ -707,7 +707,7 @@
         Chronology chrono = instant.getChronology();
         long instantLocal = instantMillis + chrono.getZone().getOffset(instantMillis);
         chrono = selectChronology(chrono);
-        int defaultYear = chrono.year().get(instantLocal);
+        int defaultYear = chrono.year().get(instantMillis);
         
         DateTimeParserBucket bucket = new DateTimeParserBucket(
             instantLocal, chrono, iLocale, iPivotYear, defaultYear);
```
