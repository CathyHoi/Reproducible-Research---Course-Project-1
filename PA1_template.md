
library(ggplot2)
library(knitr)

data <- read.csv("activity.csv")

data$date <- as.POSIXct(data$date, "%Y-%m-%d")
weekday <- weekdays(data$date)
data <- cbind(data,weekday)

summary(data)

##Histogram1

total_steps <- with(data, 
                    aggregate(steps, by = list(date), 
                              FUN = sum, na.rm = TRUE))
names(total_steps) <- c("date", "steps")
hist(total_steps$steps, 
     main = "Total number of steps taken per day", 
     xlab = "Total steps taken per day", 
     col = "purple", ylim = c(0,20), 
     breaks = seq(0,25000, by=2500))

# Mean of the total number of steps per day
mean(total_steps$steps)

# Median of the total number of steps per day
median(total_steps$steps)

##Plot1
average_steps <- aggregate(data$steps, 
                                    by=list(data$interval), 
                                    FUN=mean, na.rm=TRUE)
names(average_steps) <- c("interval", "mean")
plot(average_steps$interval, 
     average_steps$mean, 
     type = "l", 
     col="purple", 
     lwd = 2, 
     xlab="Interval", ylab="Average number of steps", 
     main="Average number of steps per intervals")

# The 5-minute interval that contain the max number of steps
average_steps[which.max(average_steps$mean), ]$interval

##Histogram 2
# Impute missing values

# Calculate the total number of missing values
sum(is.na(data$steps))

# Fill in all missing values
imputed_steps <- average_steps$mean[
                      match(data$interval,
                            average_steps$interval)
                      ]

# Create a new dataset with missing data filled in
data_imputed <- transform(data, steps = ifelse(is.na(data$steps), yes = imputed_steps, no = data$steps))
total_steps_imputed <- aggregate(steps ~ date, data_imputed, sum)
names(total_steps_imputed) <- c("date", "daily_steps")

# Plot the total number of steps per day
# Calculate and report the mean and median
# Using a histogram
hist(total_steps_imputed$daily_steps, 
     col = "purple", 
     xlab = "Total steps per day", ylim = c(0,30), 
     main = "Total number of steps taken each day", 
     breaks = seq(0,25000,by=2500))

# Mean of the total number of steps per day
mean(total_steps_imputed$daily_steps)

# Median of the total number of steps per day
median(total_steps_imputed$daily_steps)

##Plot 2
# Create a new variable indicating the weekday or weekend day
# Create variable with date in correct format
data_imputed$Newdate <- as.Date(data_imputed$date, format = "%Y-%m-%d")
# create a variable with weekdays name
data_imputed$Weekday <- weekdays(data_imputed$Newdate)
# create a new variable indicating weekday or weekend
data_imputed$Type <- ifelse(data_imputed$Weekday=='Saturday' | data_imputed$Weekday=='Sunday', 
                               'weekend','weekday')
# see first 10 values
head(data_imputed, n=10)

data_imputed$Type

# create table with steps per time across weekdaydays or weekend days
average_steps_Type <- aggregate(steps~interval+Type,data=data_imputed,FUN=mean,na.action=na.omit)
# variable time (more comprensible for the graph axis)
average_steps_Type$time <- average_steps$interval/100
# draw the line plot
plot <- ggplot(average_steps_Type, aes(time, steps))
plot+geom_line(col="purple") +
               ggtitle("Average steps per time interval: weekdays vs. weekends") +
               xlab("Time") + ylab("Steps") + 
               theme(plot.title = element_text(face="bold", size=12)) + 
               facet_grid(Type ~ .)

knit("PA1_template.Rmd")
 
