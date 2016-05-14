Reproducible Research Assignment
================================

Loading and preprocessing the data
----------------------------------

    act <- read.csv("activity.csv",header=TRUE,as.is=TRUE)
    act$date <- as.Date(act$date,"%Y-%m-%d")
    str(act)

    ## 'data.frame':    17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...

What is mean total number of steps taken per day?
-------------------------------------------------

-   Calculate the total number of steps taken per day

<!-- -->

    dailyAct <- aggregate(act$steps,by=list(act$date),sum,na.rm=TRUE)
    names(dailyAct) <- c("Date","TotalSteps")
    str(dailyAct)

    ## 'data.frame':    61 obs. of  2 variables:
    ##  $ Date      : Date, format: "2012-10-01" "2012-10-02" ...
    ##  $ TotalSteps: int  0 126 11352 12116 13294 15420 11015 0 12811 9900 ...

-   Make a histogram of the total number of steps taken each day

<!-- -->

    plot(TotalSteps~Date,dailyAct,type="h")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)<!-- -->

-   Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    mean(dailyAct$TotalSteps)

    ## [1] 9354.23

    median(dailyAct$TotalSteps)

    ## [1] 10395

What is the average daily activity pattern?
-------------------------------------------

-   Calculate the average number of steps taken across the 5-minute
    interval (x-axis)

<!-- -->

    intervalAct <- aggregate(act,by=list(act$interval),mean,na.rm=TRUE)
    str(intervalAct)

    ## 'data.frame':    288 obs. of  4 variables:
    ##  $ Group.1 : int  0 5 10 15 20 25 30 35 40 45 ...
    ##  $ steps   : num  1.717 0.3396 0.1321 0.1509 0.0755 ...
    ##  $ date    : Date, format: "2012-10-31" "2012-10-31" ...
    ##  $ interval: num  0 5 10 15 20 25 30 35 40 45 ...

-   Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute
    interval (x-axis) and the average number of steps taken, averaged
    across all days (y-axis)

<!-- -->

    plot(intervalAct$interval,intervalAct$steps,type="l")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)<!-- -->

-   Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

-----&gt; Interval 835 contains the maximum number of steps
(206.1698113) when averaged across all days.

Impute missing values
---------------------

-   Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with 𝙽𝙰s)

<!-- -->

    sum(is.na(act))

    ## [1] 2304

-   Fill in the missing values with the mean for the interval averaged
    across all days

<!-- -->

    actImp <- transform(act, steps = ifelse(is.na(act$steps), intervalAct$steps, act$steps))
    sum(is.na(actImp))

    ## [1] 0

-   Make a histogram of the total number of steps taken each day (with
    imputed data)

<!-- -->

    dailyActImp <- aggregate(actImp$steps,by=list(act$date),sum,na.rm=TRUE)
    names(dailyActImp) <- c("Date","TotalSteps")
    plot(TotalSteps~Date,dailyActImp,type="h")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)<!-- -->

-   Calculate and report the mean and median total number of steps taken
    per day.

<!-- -->

    mean(dailyActImp$TotalSteps)

    ## [1] 10766.19

    median(dailyActImp$TotalSteps)

    ## [1] 10766.19

-   Do these values differ from the estimates from the first part of the
    assignment? What is the impact of imputing missing data on the
    estimates of the total daily number of steps?

-----&gt; The mean before imputing values was : 9354.2295082 and after
imputing the mean was 1.076618910^{4}.

-----&gt; The median before imputing values was : 10395 and after
imputing the median was 1.076618910^{4}.

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

-   Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    actImp$isWeekend <- factor(ifelse(weekdays(actImp$date)=="Saturday" | weekdays(actImp$date)=="Sunday","Weekend","Weekday"))

-   Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).

<!-- -->

    intervalActW <- aggregate(steps ~ interval + isWeekend,actImp,mean)

    ggplot(intervalActW, aes(x = interval, y = steps)) + facet_grid(isWeekend ~ .) + geom_line(colour = "blue")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-15-1.png)<!-- -->
