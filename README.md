# Reproducible-Research---Course-Project-1

1.	The codes for reading in the dataset and/or processing the data are:

data <- read.csv("activity.csv")

data$date <- as.POSIXct(data$date, "%Y-%m-%d")
weekday <- weekdays(data$date)
data <- cbind(data,weekday)

summary(data)

2.	“Histogram for Question1” shows histogram of the total number of steps taken.
3.	Mean and median number of steps taken each day are 9354.23 and 10395, respectively.
4.	“Plot for Question2” shows time series plot of the average number of steps taken.
5.	The 5-minute interval that, on average, contains the maximum number of steps is 835
6.	The code to describe and show a strategy for imputing missing data are:
-> Calculate the total number of missing values
sum(is.na(data$steps))

-> Fill in all missing values
imputed_steps <- average_steps$mean[
                      match(data$interval,
                            average_steps$interval)
                      ]

-> Create a new dataset with missing data filled in
data_imputed <- transform(data, steps = ifelse(is.na(data$steps), yes = imputed_steps, no = data$steps))
total_steps_imputed <- aggregate(steps ~ date, data_imputed, sum)
names(total_steps_imputed) <- c("date", "daily_steps")
7.	“Histogram for Question3” shows histogram of the total number of steps taken each day after missing values are imputed
8.	“Plots for Question4” shows the panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends
9.	The R code provided will be able to reproduce the results in the report. 
