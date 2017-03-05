Loading packages
----------------

    library(ggplot2)
    library(scales)
    library(Hmisc)

    ## Loading required package: lattice

    ## Loading required package: survival

    ## Loading required package: Formula

    ## 
    ## Attaching package: 'Hmisc'

    ## The following objects are masked from 'package:base':
    ## 
    ##     format.pval, round.POSIXt, trunc.POSIXt, units

Load data
---------

Mean total number of steps taken per day
========================================

    stepsByDay <- tapply(activityData$steps, activityData$date, sum, na.rm=TRUE)

1. Make a histogram of the total number of steps taken each day
---------------------------------------------------------------

    qplot(stepsByDay, xlab='Total steps per day', ylab='Frequency using binwith 500', binwidth=500)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)
\#\# Calculate and report the mean and median total number of steps
taken per day

    stepsByDayMean <- mean(stepsByDay)
    stepsByDayMedian <- median(stepsByDay)

average daily activity pattern
==============================

    averageStepsPerTimeBlock <- aggregate(x=list(meanSteps=activityData$steps), by=list(interval=activityData$interval), FUN=mean, na.rm=TRUE)

Make a time series plot
-----------------------

    ggplot(data=averageStepsPerTimeBlock, aes(x=interval, y=meanSteps)) +
        geom_line() +
        xlab("5-minute interval") +
        ylab("average number of steps taken")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)
\#\# 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps

    mostSteps <- which.max(averageStepsPerTimeBlock$meanSteps)
    timeMostSteps <-  gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", averageStepsPerTimeBlock[mostSteps,'interval'])

Missing values
==============

Calculate and report the total number of missing values in the dataset
----------------------------------------------------------------------

    numMissingValues <- length(which(is.na(activityData$steps)))

Number of missing values : 2304
-------------------------------

New dataset that is equal to the original dataset but with the missing data filled in
-------------------------------------------------------------------------------------

    activityDataImputed <- activityData
    activityDataImputed$steps <- impute(activityData$steps, fun=mean)

Make a histogram of the total number of steps taken each day
------------------------------------------------------------

    stepsByDayImputed <- tapply(activityDataImputed$steps, activityDataImputed$date, sum)
    qplot(stepsByDayImputed, xlab='Total steps per day (Imputed)', ylab='Frequency using binwith 500', binwidth=500)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)
\#\# Calculate and report the mean and median total number of steps
taken per day.

    stepsByDayMeanImputed <- mean(stepsByDayImputed)
    stepsByDayMedianImputed <- median(stepsByDayImputed)

Differences in activity patterns between weekdays and weekends
==============================================================

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day
-------------------------------------------------------------------------------------------------------------------------------------------------

    activityDataImputed$dateType <-  ifelse(as.POSIXlt(activityDataImputed$date)$wday %in% c(0,6), 'weekend', 'weekday')

Panel plot containing a time series plot
----------------------------------------

    averagedActivityDataImputed <- aggregate(steps ~ interval + dateType, data=activityDataImputed, mean)
    ggplot(averagedActivityDataImputed, aes(interval, steps)) + 
        geom_line() + 
        facet_grid(dateType ~ .) +
        xlab("5-minute interval") + 
        ylab("avarage number of steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-13-1.png)
