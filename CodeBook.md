========================
# Introduction   
from Coursera.org, Data Science, Getting and Cleaning Data, Week 4 , J Leek - instructor:  
>One of the most exciting areas in all of data science right now is wearable computing   
>see for example this article . Companies like Fitbit, Nike, and Jawbone Up are   
>racing to develop the most advanced algorithms to attract new users. The data   
>linked to from the course website represent data collected from the accelerometers   
>from the Samsung Galaxy S smartphone. A full description is available at the site   
>where the data was obtained:  
>  
>http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones  
>  
>Here are the data for the project:  
>https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip  

========================  
# Purpose  
> 1.Merge the training and the test sets to create one data set.  
  
> 2.Extract only the measurements on the mean and standard deviation for each measurement.  

> 3.Use descriptive activity names to name the activities in the data set  

> 4.Appropriately label the data set with descriptive variable names.  

> 5.From the data set in step 4, create a second, independent tidy data set with the    
>   average of each variable for each activity and each subject.  

=========================
# R Script with Definitions

## 1. Gets and Merges Data  
```R #Load R packages          
library(plyr)  
library(data.table)  
  
#set working directory  
setwd("c:/Users/bensmith/Desktop/DataScience/Getting and Cleaning Data/UCI HAR Dataset/") 
  
# Reading trainings tables into variables (xtrain, ytrain, subjecttrain):  
# xtrain <- load training observations  
xtrain <- read.table("./data/UCI HAR Dataset/train/X_train.txt")  
# ytrain <- load training ids  
ytrain <- read.table("./data/UCI HAR Dataset/train/y_train.txt")
# subjecttrain <- load training subject ids 
subjecttrain <- read.table("./data/UCI HAR Dataset/train/subject_train.txt")  

# Reading testing tables into variables (xtest, ytest, subjecttest):  
# xtest <- load test observations  
xtest <- read.table("./data/UCI HAR Dataset/test/X_test.txt")  
# ytest <- load test ids  
ytest <- read.table("./data/UCI HAR Dataset/test/y_test.txt")  
# subjecttest <- load test subject ids  
subjecttest <- read.table("./data/UCI HAR Dataset/test/subject_test.txt")  

# Reading feature vector:  
# features <- load variables (columns)
features <- read.table('./data/UCI HAR Dataset/features.txt')  

# Reading activity labels:  
# activityLabels <- load labels for type of observed activity
activityLabels = read.table('./data/UCI HAR Dataset/activity_labels.txt')  

#Merge into train/test data into single sets  
xData <- rbind(xtrain, xtest)  
yData <- rbind(ytrain, ytest)  
subjectData <- rbind(subjecttrain, subjecttest)  
```
  
## 2. Extracts measurments on the mean and std Dev for each measurement  
```R xDataMS <- xData[, grep("-(mean|std)\\(\\)", features[,2])]  
names(xDataMS) <- features[grep("-(mean|std)\\(\\)", features[,2]),2]  
```
  
## 3. Adds Descriptive activity names  
```R yData[,1] <- activityLabels[yData[,1],2]  
names(yData) <- "Activity"
```  

## 4. Labels dataset with variable names  
```R names(subjectData) <- "Subject"  

#Organize and combine into single dataset  
Data <- cbind(xDataMS, yData, subjectData)  

#define names for all variables  
names(Data) <- make.names(names(Data))  
names(Data) <- gsub('Acc',"Acceleration",names(Data))  
names(Data) <- gsub('GyroJerk',"AngularAcceleration",names(Data))  
names(Data) <- gsub('Gyro',"AngularSpeed",names(Data))  
names(Data) <- gsub('Mag',"Magnitude",names(Data))  
names(Data) <- gsub('^t',"TimeDomain.",names(Data))  
names(Data) <- gsub('^f',"FrequencyDomain.",names(Data))  
names(Data) <- gsub('\\.mean',".Mean",names(Data))  
names(Data) <- gsub('\\.std',".StandardDeviation",names(Data))  
names(Data) <- gsub('Freq\\.',"Frequency.",names(Data))  
names(Data) <- gsub('Freq$',"Frequency",names(Data))  

#demonstrate functionality  
View(Data)  
names(Data)
```  

## 5. Creates tidy data set with average of each variable for each activity and each subject  
```R Data2<-aggregate(. ~Subject + Activity, Data, mean)  
Data2<-Data2[order(Data2$Subject,Data2$Activity),]  
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
```
=========================
