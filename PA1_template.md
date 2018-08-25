---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

First load the activity.csv

```r
data<-read.csv("activity.csv")
imputed_data<-read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

```r
## aggregate data by dates
aggregate_data<-aggregate(data$steps, by=list(data$date), sum)
colnames(aggregate_data)<-c('date','steps')

## create histogram of steps
hist(aggregate_data$steps,col="green", xlab="Number of steps", main = "Total number of steps taken each day")
```

![](https://raw.githubusercontent.com/sunnychua90/RepData_PeerAssessment1/master/PA1_template_files/figure-html/aggregate%20date-1.png)<!-- -->

```r
## mean steps per day
mean(aggregate_data$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
## median steps per day
median(aggregate_data$steps,na.rm=TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

```r
## aggregate data by intervals
aggregate_interval<-aggregate(data$steps, by=list(data$interval), mean, na.rm=TRUE)
mean_interval<-mean(aggregate_interval$x)
colnames(aggregate_interval)<-c('interval','average steps')

## time series plot of 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
plot(aggregate_interval$interval,aggregate_interval$'average steps',type="l", xlab = "5 minute interval", ylab = "Average steps", main = "Time series plot of average number of steps taken for 5-min interval")
```

![](https://raw.githubusercontent.com/sunnychua90/RepData_PeerAssessment1/master/PA1_template_files/figure-html/aggregate%20intervals-1.png)<!-- -->

```r
## obtain interval with max number of steps
apply(aggregate_interval, MARGIN = 2, function(x) max(x, na.rm=TRUE))
```

```
##      interval average steps 
##     2355.0000      206.1698
```

## Imputing missing values

```r
## count number of rows with NAs
sum(is.na(data$steps))
```

```
## [1] 2304
```

```r
## mean imputation of NA with the average of steps for 5 minute interval
imputed_data[is.na(imputed_data)] <- mean_interval

## aggregate data by dates
aggregate_imputed_data<-aggregate(imputed_data$steps, by=list(imputed_data$date), sum)
colnames(aggregate_imputed_data)<-c('date','steps')

## create histogram of steps
hist(aggregate_imputed_data$steps,col="blue",xlab="Number of steps", main="Total number of steps taken each day (imputed values)")
```

![](https://raw.githubusercontent.com/sunnychua90/RepData_PeerAssessment1/master/PA1_template_files/figure-html/count%20NA-1.png)<!-- -->

```r
## mean steps per day (imputed data)
mean(aggregate_imputed_data$steps)
```

```
## [1] 10766.19
```

```r
## mean steps per day (without imputed data)
mean(aggregate_data$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
## median steps per day (imputed data)
median(aggregate_imputed_data$steps)
```

```
## [1] 10766.19
```

```r
## median steps per day (without imputed data)
median(aggregate_data$steps,na.rm=TRUE)
```

```
## [1] 10765
```
## Are there differences in activity patterns between weekdays and weekends?

```r
imputed_data$date<-as.Date(imputed_data$date)
#create a vector of weekdays
weekdays1 <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')
#Use `%in%` and `weekdays` to create a logical vector, convert to `factor` and specify the `levels/labels`
imputed_data$Day <- factor((weekdays(imputed_data$date) %in% weekdays1), levels=c(FALSE, TRUE), labels=c('Weekend', 'Weekday'))

## plot chart
library(lattice)
aggregate_interval_imputed_data<-aggregate(imputed_data$steps, by=list(imputed_data$interval,imputed_data$Day), mean)
colnames(aggregate_interval_imputed_data)<-c('interval','day','steps')
xyplot(steps ~ interval | day, data = aggregate_interval_imputed_data, type = "l", ylab="Number of steps", layout = c(1,2))
```

![](https://raw.githubusercontent.com/sunnychua90/RepData_PeerAssessment1/master/PA1_template_files/figure-html/activity%20diff-1.png)<!-- -->
