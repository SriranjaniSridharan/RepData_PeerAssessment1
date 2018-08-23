 

### 1. Code for reading in the dataset

    dt<-read.csv("D:/Coursera/Data Science/activity.csv")

### 2. Histogram of the total number of steps taken each day

    d1<-dt %>% group_by(date) %>% summarise(steps=sum(steps))
    hist(d1$steps)

![](PA1_template_files/figure-markdown_strict/histogram-1.png)

### 3. Mean and median number of steps taken each day

    mean(d1$steps,na.rm=TRUE)

    ## [1] 10766.19

    median(d1$steps,na.rm=TRUE)

    ## [1] 10765

### 4. Time series plot of the average number of steps taken

    d2<-dt %>% group_by(interval) %>% summarise(steps=mean(steps,na.rm=TRUE))
    plot(d2$interval,d2$steps,type="l")

![](PA1_template_files/figure-markdown_strict/time%20series%20plot-1.png)

### 5. The 5-minute interval that, on average, contains the maximum number of steps

    d2[d2$steps==max(d2$steps),]

    ## # A tibble: 1 x 2
    ##   interval steps
    ##      <int> <dbl>
    ## 1      835  206.

Maximum number of steps is at interval 835

### 6.Code to describe and show a strategy for imputing missing data

The total number of missing values in the dataset

    sum(is.na(dt$steps))

    ## [1] 2304

We would replace the missing values(NAs) with the average of steps taken
in the 5-minute interval averaged across all days.

Creating a new data set with the missing data filled in -

    newdt=merge(dt,d2,by="interval")
    newdt[is.na(newdt$steps.x),"steps.x"]<-newdt[is.na(newdt$steps.x),"steps.y"]
    newdt$steps<-newdt$steps.x
    newdt$steps.x<-NULL
    newdt$steps.y<-NULL

### 7.Histogram of the total number of steps taken each day after missing values are imputed

    newd1<-newdt %>% group_by(date) %>% summarise(steps=sum(steps))
    hist(newd1$steps)

![](PA1_template_files/figure-markdown_strict/new_histogram-1.png)

Mean and median number of steps taken each day after missing values are
imputed

    mean(newd1$steps)

    ## [1] 10766.19

    median(newd1$steps,na.rm=TRUE)

    ## [1] 10766.19

### 8.Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

Adding weekday column to the data, Creating two new datasets - dtwe for
weekend data and dtwd for weekdays data

    newdt$weekday<-weekdays(as.Date(newdt$date))
    dtwe<-newdt[newdt$weekday %in% c("Saturday","Sunday"),]
    dtwd<-newdt[!(newdt$weekday %in% c("Saturday","Sunday")),]
    head(dtwd)

    ##   interval       date    steps   weekday
    ## 1        0 2012-10-01 1.716981    Monday
    ## 2        0 2012-11-23 0.000000    Friday
    ## 4        0 2012-11-06 0.000000   Tuesday
    ## 6        0 2012-11-15 0.000000  Thursday
    ## 8        0 2012-11-16 0.000000    Friday
    ## 9        0 2012-11-07 0.000000 Wednesday

    head(dtwe)

    ##    interval       date    steps  weekday
    ## 3         0 2012-10-28 0.000000   Sunday
    ## 5         0 2012-11-24 0.000000 Saturday
    ## 7         0 2012-10-20 0.000000 Saturday
    ## 10        0 2012-11-25 0.000000   Sunday
    ## 11        0 2012-11-04 1.716981   Sunday
    ## 19        0 2012-11-18 0.000000   Sunday

Panel plot

    dtwe<-dtwe %>% group_by(interval) %>% summarise(steps=mean(steps,na.rm=TRUE))
    dtwd<-dtwd %>% group_by(interval) %>% summarise(steps=mean(steps,na.rm=TRUE))
    par(mfrow=c(1,2))
    plot(dtwd$interval,dtwd$steps,type="l",ylim=c(0,250),xlab="Inteval",ylab="Steps")
    title("Weekdays")
    plot(dtwe$interval,dtwe$steps,type="l",ylim=c(0,250),xlab="Inteval",ylab="Steps")
    title("Weekends")

![](PA1_template_files/figure-markdown_strict/panel%20plot-1.png)
