R Markdown
----------

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00

Including Plots
---------------

You can also embed plots, for example:

![](PA1_template_files/figure-markdown_github/pressure-1.png)<!-- -->

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.

``` r
library(plyr)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:plyr':
    ## 
    ##     arrange, count, desc, failwith, id, mutate, rename, summarise,
    ##     summarize

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(knitr)
library(ggplot2)

#remove missing values
#tapply aggregate to sum over date
file = read.csv("./activity.csv", header=TRUE)
file$date <- as.Date(file$date)
new_file <- file[complete.cases(file), ]
dailySteps <- tapply(new_file$steps, new_file$date, sum, na.rm=TRUE)

special <- unique(dailySteps)
hist(special, 25, main="Steps per Day")
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-1-1.png)<!-- -->

``` r
avgDailySteps <- mean(special)
medDailySteps <- median(special)

activity<-tapply(new_file$steps, new_file$interval, mean)
plot(y = activity, x = names(activity), type = "l", xlab = "Every 5 minutes", main = "Daily Activity", ylab = "Avg Steps")
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-1-2.png)<!-- -->

``` r
truth_table <- activity==max(activity)
activity[truth_table]
```

    ##      835 
    ## 206.1698

``` r
#of NA's
dim(file)[1] - dim(new_file)[1]
```

    ## [1] 2304

``` r
#replace NA's -> be careful....daily is aggregated!!
file$steps[which(is.na(file$steps))] <- mean(file$steps, na.rm=T)
sum(is.na(file))
```

    ## [1] 0

``` r
#draw after replace
new_dailySteps <- tapply(file$steps, file$date, sum)
hist(new_dailySteps, 25, main="Steps per Day")
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-1-3.png)<!-- -->

``` r
# weekends vs weekdays
weekday <- file[!(weekdays(file$date) %in% c('Saturday', 'Sunday')),]
weekend <- file[weekdays(file$date) %in% c('Saturday', 'Sunday'),]

wd_activity <- tapply(weekday$steps, weekday$interval, mean)
we_activity <- tapply(weekend$steps, weekend$interval, mean)
par(mfrow=c(2,1))
plot(wd_activity, type="l", main="Weekdays", xlab="5min Intervals", ylab="Avg Steps")
plot(we_activity, type="l", main="Weekends", xlab="5min Intervals", ylab="Avg Steps")
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-1-4.png)<!-- -->
