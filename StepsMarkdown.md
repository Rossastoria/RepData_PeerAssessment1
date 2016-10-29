#Reproducible Research, Project 1
===========================

First, load the data into R and use aggregate() to convert to a form appropriate to a histogram.


```r
data <- read.table("activity.csv", header = TRUE, sep = ",", na.strings = "NA", colClasses = c(date = "Date"))
s <- aggregate(data$steps ~ data$date, FUN = sum, na.rm = TRUE, data = data)
```

Then, create a histogram of step frequency.


```r
hist(x = s[,2], breaks = 20, col = "purple", xlab = "Daily Total Steps", ylab = "Frequency")
```

![](StepsMarkdown_files/figure-html/firstHist-1.png)<!-- -->

Also, identify the mean and median of steps taken per day.


```r
mean(s[,2])
```

```
## [1] 10766.19
```

```r
median(s[,2])
```

```
## [1] 10765
```

Now, create a timeseries plot of mean number of steps per 5 minute interval, removing NA values.


```r
average <- aggregate(data$steps ~ data$interval, data = data, FUN = mean, na.rm = TRUE)
plot(x = average[,1], y = average[,2], xlab = "Interval", ylab = "No. of Steps", type = "l")
```

![](StepsMarkdown_files/figure-html/impute-1.png)<!-- -->

That's a nice plot. Now find the interval with the max average number of steps.


```r
average[which.max(average[,2]),]
```

```
##     data$interval data$steps
## 104           835   206.1698
```

Now, let's see how many missing values there are. 

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

Let's impute values to the NA values, using a simple averaging method ...

```r
data_impute <- data
missing <- is.na(data$steps)
data_impute$steps[missing] <- average[,2][missing]
```

... and plot a new histogram, first aggregating.

```r
s_impute <- aggregate(data_impute$steps ~ data_impute$date, data = data, FUN = sum, na.rm = TRUE)
hist(x = s_impute[,2], breaks = 20, col = "purple", xlab = "Daily Total Steps_Imputed", ylab = "Frequency")
```

![](StepsMarkdown_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

Are the mean and median of the imputed data different?

```r
mean(s_impute[,2])
```

```
## [1] 10766.19
```

```r
median(s_impute[,2])
```

```
## [1] 10765.59
```

Imputing the mean to missing observations does not change the mean. In this case, imputing the mean draws the new median closer to the old mean.

Last, let's see if there's a difference in the number of steps taken on the weekday against the weekend. The following creates a new factor variable of two levels, weekday and weekend.


```r
data_impute$day <- weekdays(data_impute$date)
data_impute$day <- ifelse(data_impute$day == "Sunday" | data_impute$day == "Saturday", "Weekend", "Weekday")
data_impute$day <- as.factor(data_impute$day)
s_impute2 <- aggregate(steps ~ day + interval, FUN = mean, data = data_impute)
library(lattice)
xyplot(steps ~ interval | factor(day), layout = c(1,2), xlab = "Interval", 
         ylab = "Steps", type = "l", data = s_impute2)
```

![](StepsMarkdown_files/figure-html/weekend-1.png)<!-- -->

Looks like on the weekday, people get up and start walking around earlier than on the weekend!

author: "Astoria"
date: "Saturday, October 22, 2016"
output: html_document
---
