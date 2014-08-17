<h1>Reproducible Research - Peer Assessment Assignment 1</h1>

<p>This is the R markdown file for my peer assessment assignment 1 for Reproducible Research.  </p>

<h2>Step 1. Loading the Preprocessing the data.</h2>

<ol>
<li>Read &ldquo;activity.csv&rdquo; file by using read.csv function</li>
<li>Filter out the NA values by using complete.cases</li>
<li>Change the format of &ldquo;date&rdquo; variable into data by using as.Date</li>
</ol>

<pre><code class="r">data &lt;- read.csv(&quot;activity.csv&quot;, header=T)
tidy_data &lt;- data[complete.cases(data),]
tidy_data$date &lt;- as.Date(tidy_data$date)
</code></pre>

<h2>Step 2. What is mean total number of steps taken per day?</h2>

<ul>
<li>Make a histogram of the total number of steps taken each day by using aggregate and barplot function</li>
</ul>

<pre><code class="r">Tot_Steps_by_Day &lt;- aggregate(tidy_data$steps, list(tidy_data$date),sum)
barplot(Tot_Steps_by_Day$x, names.arg=Tot_Steps_by_Day$Group.1)
</code></pre>

<ul>
<li>Calculate and report the mean total number of steps taken per day</li>
</ul>

<pre><code class="r">mean(Tot_Steps_by_Day$x) 
</code></pre>

<ul>
<li>Calculate and report the median total number of steps taken per day</li>
</ul>

<pre><code class="r">median(Tot_Steps_by_Day$x) 
</code></pre>

<h2>Step 3. What is the average daily activity pattern?</h2>

<ul>
<li>Make a time series plot of the 5-minute interval  and the average number of steps by using aggregate and plot function</li>
</ul>

<pre><code class="r">Mean_steps_by_interval &lt;- aggregate(tidy_data$steps, list(tidy_data$interval),mean)
with(Mean_steps_by_interval, plot(Group.1, x, type=&quot;l&quot;,xlab=&quot;5-min Interval&quot;, ylab=&quot;Avg # of steps&quot;))

</code></pre>

<ul>
<li>Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?</li>
</ul>

<pre><code class="r">Mean_steps_by_interval[which(Mean_steps_by_interval$x == max(Mean_steps_by_interval$x)),1]
</code></pre>

<h2>Step 4. Imputing missing values</h2>

<ul>
<li>Calculate and report the total number of missing values in the dataset by using complete.cases function and summing the output vector</li>
</ul>

<pre><code class="r">sum(!complete.cases(data))
</code></pre>

<ul>
<li><p>Devise a strategy for filling in all of the missing values in the dataset. - I will fill the NAs with the average of that 5 min interval.</p></li>
<li><p>Create a new dataset that is equal to the original dataset but with the missing data filled in by using for-loop</p></li>
</ul>

<pre><code class="r">data_fillingNA &lt;- data
data_fillingNA$date &lt;- as.Date(data_fillingNA$date)
for (i in 1:length(data$steps)) {
  if (is.na(data_fillingNA[i,1])) {
    data_fillingNA[i,1] &lt;- as.numeric(Mean_steps_by_interval[(data_fillingNA[i,3]==Mean_steps_by_interval[,1]),2])
  }  
}
head(data_fillingNA)
</code></pre>

<ul>
<li>Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. </li>
</ul>

<pre><code class="r">Tot_Steps_by_Day_fillingNA &lt;- aggregate(data_fillingNA$steps, list(data_fillingNA$date),sum)
barplot(Tot_Steps_by_Day_fillingNA$x, names.arg=Tot_Steps_by_Day_fillingNA$Group.1)

mean(Tot_Steps_by_Day_fillingNA$x) 

median(Tot_Steps_by_Day_fillingNA$x) 
</code></pre>

<ul>
<li><p>Do these values differ from the estimates from the first part of the assignment? - No significant difference</p></li>
<li><p>What is the impact of imputing missing data on the estimates of the total daily number of steps? - No huge impact</p></li>
</ul>

<h2>Step 5. Are there differences in activity patterns between weekdays and weekends?</h2>

<ul>
<li>Create a new factor variable in the dataset with two levels - &ldquo;weekday&rdquo; and &ldquo;weekend&rdquo;.</li>
</ul>

<pre><code class="r">Weekday_of_date &lt;- weekdays(data_fillingNA$date, abbreviate = FALSE)
data_fillingNA$Weekend_or_not &lt;- vector(length=length(data_fillingNA$steps))

data_fillingNA$Weekend_or_not[Weekday_of_date==&quot;Sunday&quot; | Weekday_of_date==&quot;Saturday&quot;] &lt;- &quot;weekend&quot;
data_fillingNA$Weekend_or_not[Weekday_of_date!=&quot;Sunday&quot; &amp; Weekday_of_date!=&quot;Saturday&quot;] &lt;- &quot;weekday&quot;

data_fillingNA$Weekend_or_not &lt;- as.factor(data_fillingNA$Weekend_or_not)

</code></pre>

<ul>
<li>Make a panel plot containing a time series plot  of the 5-minute interval and the average number of steps taken by splitting into weekend factor and using base plotting system</li>
</ul>

<pre><code class="r">Mean_steps_by_interval_fillingNA &lt;- aggregate(data_fillingNA$steps, list(data_fillingNA$interval,data_fillingNA$Weekend_or_not),mean)

par(mfrow = c(2,1))
plot(Mean_steps_by_interval_fillingNA[Mean_steps_by_interval_fillingNA$Group.2==&quot;weekend&quot;,1], Mean_steps_by_interval_fillingNA[Mean_steps_by_interval_fillingNA$Group.2==&quot;weekend&quot;,3], xlab = &quot;Interval&quot;, ylab = &quot;Avg Number of steps&quot;, type=&quot;l&quot;, main=&quot;weekend&quot;, ylim=c(0,250))
plot(Mean_steps_by_interval_fillingNA[Mean_steps_by_interval_fillingNA$Group.2==&quot;weekday&quot;,1], Mean_steps_by_interval_fillingNA[Mean_steps_by_interval_fillingNA$Group.2==&quot;weekday&quot;,3], xlab = &quot;Interval&quot;, ylab = &quot;Avg Number of steps&quot;, type=&quot;l&quot;,main=&quot;weekday&quot;,ylim=c(0,250))
</code></pre>
