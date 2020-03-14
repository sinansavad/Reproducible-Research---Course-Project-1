title: "Reproducible Research: Peer Assessment 1" author: "Sinan Savad Nalakath" date: "14/3/2020" output: html_document: keep_md: yes
Loading and preprocessing the data
Load the data:
data <- read.csv("activity.csv", header=TRUE)
Show the original data:
head(data)
What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

Subset the original data to calculate the total number of steps
stepsByDay <- aggregate(steps~date, data, sum)
head(stepsByDay)
Make a histogram of the total number of steps taken each day
hist(stepsByDay$steps, col = "chocolate"
     , xlab = "Steps", main = "Total Number of Steps Per Day")
Calculate and report the mean and median total number of steps taken per day
mean1 <- mean(stepsByDay$steps)
mean1
median1 <- median(stepsByDay$steps)
median1
What is the average daily activity pattern?
Subset the original data to calculate the average number of steps taken of the 5-minute interval
averageSteps <- aggregate(steps~interval, data, mean)
head(averageSteps)
Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
with(averageSteps, plot(interval, steps
                , type="l", main="Average number of steps taken in 5-min interval"
                , col = "blue")) 
Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
Max data is:

max <- max(averageSteps$steps)
max
The interval contains max data is:

subsetData <- subset(averageSteps, steps == max)
subsetData$interval
Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
colSums(is.na(data))
Create a new column "newSteps" contains the average steps in each interval
data$newSteps <- averageSteps$steps
head(data)
Fill in the missing data
data$steps[is.na(data$steps)] <- data$newSteps
head(data)
Create a new dataset that is equal to the original dataset but with the missing data filled in
data$newSteps <- NULL
newdata <- data
head(newdata)
Subset in the new data set to calculate the total number of steps per day
sumdata <- aggregate(steps~date, newdata, sum)
head(sumdata)
Make a histogram of the total number of steps taken each day
hist(sumdata$steps, col = "blue"
     , xlab = "Steps"
     , main = "Total Number of Steps Per Day (with the missing data filled in)")
Calculate and report the mean and median total number of steps taken per day
mean2 <- mean(sumdata$steps)
mean2
median2 <- median(sumdata$steps)
median2
Calculate the differences of the mean and median between the first and second part
mean2 - mean1
median2 - median1
Are there differences in activity patterns between weekdays and weekends?
For this part, I do not use the weekdays() function, instead I use the isWeekday() function from the timeDate package.

Install and load the timeDate package
Please install the package first:

install.packages("timeDate")

library(timeDate)
Create a new column, and use the isWeekday() function to check if the date is weekday or weekend
newdata$Weekday <- isWeekday(newdata$date)
head(newdata)
Subset and calculate the average steps for weekday and weekend
Weekday

weekday <- subset(newdata, newdata$Weekday == "TRUE")
weekdayMean <- aggregate(steps ~ interval, data = weekday, mean)
head(weekdayMean)
Weekend

weekend <- subset(newdata, newdata$Weekday == "FALSE")
weekendMean <- aggregate(steps ~ interval, data = weekend, mean)
head(weekendMean)
Make the panel plot to calculate the average number of steps taken for weekday and weekend
layout(matrix(c(1,1,2,2), 2, 2, byrow = TRUE))
plot(weekdayMean$interval, weekdayMean$steps
     , xlab = "interval", ylab = "Number of steps"
     , main ="Weekday", col ="blue", type="l") 
plot(weekendMean$interval, weekendMean$steps
     , xlab = "interval", ylab = "Number of steps"
     , main ="Weekend", col ="red", type="l")
