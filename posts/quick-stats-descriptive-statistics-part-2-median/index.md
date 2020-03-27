<html><body><h2>
	Introduction
</h2>

<p>
	In this next post on descriptive statistics, the median will be discussed. In <a href="http://tylermarrs.com/quick-stats-descriptive-statistics-part-1-mean/" target="_blank">part 1 of this blog series we learned about the mean (average)</a> and found out that it can easily be skewed by large or small values. Both the median and mean are "measures of central tendency". Central tendency is essentially the center position of a distribution for a data set. Now that you are refreshed, we can discuss the median.
</p>

<h2>
	Median
</h2>

<p>
	The median is simple to calculate. First, we must arrange the values from least to greatest. Next, we pick the center value - this is our median when there is an odd number of values. When there are an even number of values we simply add the two center values together and divide by 2.
</p>

<p>
	Here are examples of both odd and even cases:
</p>

<p>
	<strong>Odd number of values</strong>
</p>

<pre>
<code>1, 2, 3, 4, 5
Median = 3</code></pre>

<p>
	<strong>Even number of values</strong>
</p>

<pre>
<code>1, 2, 3, 4
Median = (2 + 3) / 2 = 2.5</code></pre>

<h2>
	Median vs. Mean
</h2>

<p>
	As you can see the median is not influenced by outliers. It provides the central value that most of us tend to think of as the average (mean). You might ask when you would use one over the other. This question is difficult to answer without context. In some situations it may make more sense to use one or both. Let's look at some grades for 10 random students to see how the mean and median look.
</p>

<p>
	The raw values are as follows:
</p>

<pre>
<code>20, 20, 25, 27, 29, 37, 45, 55, 90, 100</code></pre>

<p style="text-align: center;">
	<img alt="" class="size-full wp-image-114 aligncenter" height="299" src="/wp-content/uploads/2017/01/exam_grades_median_mean.png" style="" title="" width="598">
</p>

<p>
	In the plot, shown above, the blue line represents the mean - 44.8%. The green line represents the median - 33%. Clearly there is a difference between these two measures of central point.
</p>

<h2>
	R
</h2>

<p>
	To calculate the median in R - simply use the built in function.
</p>

```r
grades &lt;- c(20, 20, 25, 27, 29, 37, 45, 55, 90, 100)
median_grade &lt;- median(grades)
```

<h2>
	Python
</h2>

<p>
	To calculate the median in Python we use Numpy. Import Numpy and use the median function.
</p>

```python
import numpy as np
grades = [20, 20, 25, 27, 29, 37, 45, 55, 90, 100]
median_grade = np.median(grades)
```

<h2>
	Conclusion
</h2>

<p>
	In this post we discussed the median and how it differentiates from the mean. We also established that context is important when deciding to use one versus the other. The mean and median are important values used in statistics and our every day lives.
</p>

<p>
	The next article will discuss the mode - another useful descriptive statistic.
</p>
</body></html>