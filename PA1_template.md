---
title: "Reproducible research Ilka van Vliet"
output:
  html_document: default
  word_document: default
---

```{r} 
#read file and process
library(readr)
library(tidyr)
library(chron)
library(lubridate)
library(ggplot2)
activity <- read_csv("activity.csv")
```

clean up and process data
```{r} 
drop_na(activity)
steps_day <- aggregate(steps ~ date, activity, sum)
```

Histogram total number steps each day, and find the mean=10766 median=10765
```{r}
attach(activity)
hist(steps_day$steps)
summary(steps_day)
```

http://127.0.0.1:46776/chunk_output/s/9BD1B687/ci9ool7goh8j6/000003.png

Steps per interval
```{r}
steps_interval <- aggregate(steps ~ interval, activity, mean)
plot(steps_interval$interval,steps_interval$steps, xlab="Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")
max_interval <- steps_interval[which.max(steps_interval$steps),1]
summary(max_interval)
```

Number of NAs, mean 1177.5 and median 1177.5, the impact is that the interval drops 
```{r}
sum(is.na(activity))
NAmean <- function(x) replace(x, is.na(x), mean(x, na.rm = TRUE))
activity[] <- lapply(activity, NAmean)
attach(activity)
hist(steps_day$steps)
summary(activity[])
```

Make column with weekday or weekend
```{r} 
library(lubridate)
activity$dayofweek <- ifelse(is.weekend(activity$date), "weekend", "weekday")
meaninterval_new<- aggregate(steps ~ interval + dayofweek, activity, FUN=mean)
ggplot(meaninterval_new, aes(x=interval, y=steps)) + 
  geom_line(color="blue", size=1) + 
  facet_wrap(~dayofweek, nrow=2) +
  labs(x="\nInterval", y="\nNumber of steps")
```


