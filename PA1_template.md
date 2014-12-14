Reproducible Research Peer assesement Project 1
=================================================
**Loading and preprocessing the data**

```r
library(data.table)
data <- read.csv("activity.csv")
head(data)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

### Question 1 What is mean total number of steps taken per day?
**Aggregate steps per day, then make a histogram. It should be a normal distributed plot**

```r
dt <- data.table(data,key="date")
sumSteps <- dt[,lapply(.SD, sum), by=date]
hist(sumSteps$steps, main = "Histogram of total steps per day",xlab="steps per day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

**Calculate the mean and median total number of steps taken per day**

```r
mean <- mean(sumSteps$steps,na.rm=TRUE)
mean
```

```
## [1] 10766.19
```

```r
median <- median(sumSteps$steps,na.rm=TRUE)
median
```

```
## [1] 10765
```
#### *The mean of the total number of steps taken each day is 10766*
#### *The median of the total number of steps taken each day is 10765*

### Question 2 What is the average daily activity pattern?
**Make a time series plot of the  5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)**


```r
dt2 <- data.table(data,key="interval")
Stepsby5min <- dt2[,lapply(.SD, mean,na.rm=TRUE), by=interval]
plot(Stepsby5min$interval,Stepsby5min$steps,type="l",xlab="interval", ylab="average number of steps per interval")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
max <- max(Stepsby5min$steps)
Stepsby5min$interval[which(Stepsby5min$steps==max)]
```

```
## [1] 835
```
#### *the 835 interval contians the maimum number of steps*

### Question 3 Imputing missing values
**1. The total number of missing values in the dataset**

```r
table(is.na(data$steps))
```

```
## 
## FALSE  TRUE 
## 15264  2304
```

**2. Filling in all of the missing values in the dataset with the **mean for the 5-minue interval**
   A new dataset data2 is generated with NA value replaced **

```r
data2 <- as.data.frame(data)
data2[is.na(data)]<-mean/288
```
**Histogram of the total number of steps taken each day**

```r
dt2 <- data.table(data2,key="date")
sumSteps2 <- dt2[,lapply(.SD, sum), by=date]
hist(sumSteps2$steps, main = "Histogram of total steps per day with NA value replaced",xlab="steps per day")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

**3.Calculate the mean and median total number of steps taken per day**

```r
mean <- mean(sumSteps2$steps,na.rm=TRUE)
mean
```

```
## [1] 10766.19
```

```r
median <- median(sumSteps2$steps,na.rm=TRUE)
median
```

```
## [1] 10766.19
```
#### *The mean of the total number of steps taken each day is 10766*
#### *The median of the total number of steps taken each day is 10766*
#### *It seems like by replacing the NA value with the interval mean steps didnot change the histogram, and it increase the median to the same value as the mean, which makes the distribution more normal.*

### Question 4 Are there differences in activity patterns between weekdays and weekends?
**Convert the date into day of week, then reassign into two levels corresponding to either weekday or weekend.**

```r
date <- data[, "date"]
date <- as.Date(date)
dayofweek <- weekdays(date)
data <- cbind(data,dayofweek)
levels(data$dayofweek)
```

```
## [1] "Friday"    "Monday"    "Saturday"  "Sunday"    "Thursday"  "Tuesday"  
## [7] "Wednesday"
```

```r
levels(data$dayofweek) <- c("Weekday","Weekday","Weekend","Weekend","Weekday","Weekday","Weekday")
levels(data$dayofweek)
```

```
## [1] "Weekday" "Weekend"
```
**Subset data by two different level**

```r
weekday <- data[which(data$dayofweek=="Weekday"),]
weekend <- data[which(data$dayofweek=="Weekend"),]

weekday <- data.table(weekday,key="interval")
weekdaypattern <- weekday[,lapply(.SD, mean,na.rm=TRUE), by=interval]

weekend <- data.table(weekend,key="interval")
weekendpattern <- weekend[,lapply(.SD, mean,na.rm=TRUE), by=interval]

par(mfrow=c(2,1),mar=c(4.0,4.5,1,1))
plot(weekdaypattern$interval,weekdaypattern$steps,type="l",xlab="interval",main="Weekday pattern",ylab="steps")
plot(weekendpattern$interval,weekendpattern$steps,type="l",xlab="interval",main="Weekend pattern",ylab="steps")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 
#### *It appears that during the weekend, the average steps are lower and more evenly distributed throughout out the day.*
