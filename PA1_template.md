R Markdown
----------

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

Including Plots
---------------

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.

``` r
##Loading and preprocessing the data

activitydata <- read.csv("~/Downloads/activity 2.csv",sep = ",",header = TRUE)
subactivity <- subset(activitydata,!(is.na(activitydata$steps)))

##calculate the total number of steps taken per day
totalSteps <- aggregate(steps ~ date, data = subactivity, sum)

##histogram of total number of steps taken per day
hist(totalSteps$steps,main = "Histogram of total steps",xlab = "Total Daily Steps",col = "blue")
```

![](PA1_template_files/figure-markdown_github/Activity%20monitoring%20data-1.png)

``` r
#Mean and Median of Total steps taken per day
Msteps <- mean(totalSteps$steps) 
median(totalSteps$steps)
```

    ## [1] 10765

``` r
## calculate the mean of daily activity
averageactivity <- aggregate(steps~interval,data = subactivity,mean, na.rm = TRUE)

## plot the average daily activity
plot(steps~interval , data = averageactivity, type = "l", main = "Average Daily Activity Pattern",
     xlab = "5-min Interval", ylab = "Average Daily Steps", col = "Red")
```

![](PA1_template_files/figure-markdown_github/Activity%20monitoring%20data-2.png)

``` r
## calculate the total number of missing values
sum(is.na(activitydata$steps))
```

    ## [1] 2304

``` r
##create a newdata set merging the activity dataframe and average activity data
newactivity <- merge(activitydata, averageactivity, by = 'interval', all.y = F)

##fill the NA values with averages rounding up for integers
newactivity$steps.x[is.na(newactivity$steps.x)] <- as.integer(
  round(newactivity$steps.y[is.na(newactivity$steps.x)]))

##calculate the total number of steps taken per day
newtotalSteps <- aggregate(steps.x~date, data = newactivity,sum)

##Make a histogram of the total number of steps taken each day 
hist(newtotalSteps$steps.x,main = "Histogram of new total steps taken",
     xlab = "New Total Steps", col = "purple")

## calculate the mean and median of new total steps
mean(newtotalSteps$steps.x)
```

    ## [1] 10765.64

``` r
median(newtotalSteps$steps.x)
```

    ## [1] 10762

``` r
##Create a new factor variable in the dataset with two levels – 
##“weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
weekend <- weekdays(as.Date(newactivity$date)) %in% c("Saturday", "Sunday")
newactivity$daytype <- "weekday"
newactivity$daytype[weekend == TRUE] <- "weekend"
newactivity$daytype <- as.factor(newactivity$daytype)

##calculate average number of steps taken, averaged across all weekday days or weekend days
newinterval <- aggregate(steps.x ~ interval + daytype, newactivity, mean)


## plot differences in activity patterns between weekdays and weekends


library(lattice)
```

![](PA1_template_files/figure-markdown_github/Activity%20monitoring%20data-3.png)

``` r
xyplot(
  steps.x ~ interval | daytype,
  newinterval,
  type = "l",
  layout = c(1,2),
  main = "Weekend and Weekday Activity Pattern",
  xlab = "Interval",
  ylab = "Average Number of Steps Taken"
)
```

![](PA1_template_files/figure-markdown_github/Activity%20monitoring%20data-4.png)
