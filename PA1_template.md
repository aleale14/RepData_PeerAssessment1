---
title: "Assignment 1"
author: "AA"
output: html_document
---

##1. Loading and preprocessing the data

It is assumed that the dataset "Activity monitoring data" is located in the active working directory.

The data is read into the variable *data* via a call to the read.csv() function


```r
data<-read.csv("activity.csv")
```

To facilitate data handling in the following, the *data* variable is transformed into a dataframe

```r
data<-data.frame(Steps=data$steps,Date=data$date,Interval=data$interval)
```

and a separate, clean dataframe is created in which all records with NA steps are dropped.

```r
data_clean<-subset(data,Steps>=0)
```


##2. Mean total number and steps taken per day

To calculate the total number of steps taken per day, a simple aggregation over the *data_clean$Date* column is performed and stored in the *tot_steps_clean* variable


```r
tot_steps_clean<-aggregate(data_clean$Steps,by=list(data_clean$Date),FUN=sum)
```

The first column of the *tot_steps_clean* variable includes the date and the second (*tot_steps_clean$x*) the totalled steps per day.
The data is visualized with an histogram using the code below



```r
hist(tot_steps_clean$x,col=2,main="Total Steps per Day",ylab="Frequency",xlab="Number of Steps")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

From the *tot_steps_clean$x* variable, mean and median values are calculated using the functions _mean()_ and _median()_.


```r
mean(tot_steps_clean$x)
```

```
## [1] 10766.19
```

```r
median(tot_steps_clean$x)
```

```
## [1] 10765
```

##3. Average daily activity pattern

To calculate the average number of steps taken per interval of the day, a simple aggregation over the *data_clean$Interval* column is performed and stored in the *avg_steps_clean* variable


```r
avg_steps_clean<-aggregate(data_clean$Steps,by=list(data_clean$Interval),FUN=mean)
```

The data is visualized with a plot using the following commands.


```r
plot(avg_steps_clean$Group.1,avg_steps_clean$x,type="l",xlab="Time interval",ylab="Average number of steps per interval")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 

The interval containing on average the maximum number of steps is given by:


```r
avg_steps_clean[avg_steps_clean[2]==max(avg_steps_clean[2])][1]
```

```
## [1] 835
```

##4. Imputing missing values

The amount of records with missing values can be retrieved from the *data$Steps* variable summing up all records coded as NA.


```r
sum(is.na(data$Steps))
```

```
## [1] 2304
```

Those values are being substituted in a new dataset *data_mod* with the average number of steps for the given interval included in the *avg_steps_clean* variable.


```r
data_mod<-data
data_mod$Steps[is.na(data_mod$Steps)]<-avg_steps_clean$x[avg_steps_clean[1]==data_mod$Interval]
```

To calculate the total number of steps taken per day, a simple aggregation over the *data_mod$Date* column is performed and stored in the *tot_steps_mod* variable


```r
tot_steps_mod<-aggregate(data_mod$Steps,by=list(data_mod$Date),FUN=sum)
```

The first column of the *tot_steps_mod* variable includes the date and the second (*tot_steps_clean$x*) the totalled steps per day.
The data is visualized with an histogram using the code below



```r
hist(tot_steps_mod$x,col=2,main="Total Steps per Day",ylab="Frequency",xlab="Number of Steps")
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png) 

From the *tot_steps_mod$x* variable, mean and median values are calculated using the functions _mean()_ and _median()_.


```r
mean(tot_steps_mod$x)
```

```
## [1] 10766.19
```

```r
median(tot_steps_mod$x)
```

```
## [1] 10766.19
```

When comparing those results with the ones obtained in Step 1, we can notice that there is no impact on the mean and an increase of the median.

##5. Activity patterns
A new extended dataset is created starting from *data_mod* to include in a fourth column the weekday / weekend label leveraging on a support *Weekday* variable in which the days of the week are captured and then relabbeled.


```r
Weekday<-weekdays(as.POSIXlt(data_mod$Date))
Weekday<-ifelse ((Weekday==("Samstag")|Weekday=="Sonntag"), Weekday<-"weekend", Weekday<-"weekday")
data_tot<-cbind(data_mod,Weekday)
```

The average number of steps taken per 5-minute interval in the weekend or in the weekdays are calculated and stored in the variable *avg_steps_tot*


```r
avg_steps_tot<-aggregate(data_tot$Steps,by=list(data_tot$Interval,data_tot$Weekday),FUN=mean)
```
Those average values are then plotted using the ggplot system in a panel graph.

```r
library (ggplot2)
ggplot(avg_steps_tot,aes(Group.1,x))+geom_line()+facet_grid(. ~ Group.2)+ labs(x="Time interval",y="Average steps")
```

![plot of chunk unnamed-chunk-17](figure/unnamed-chunk-17-1.png) 
