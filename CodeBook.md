Data Source  
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

# 1. Get and Merge Data  
```R #Load Libraries          
library(plyr)  
library(data.table)  
  
#set working directory  
setwd("c:/Users/bensmith/Desktop/DataScience/Getting and Cleaning Data/UCI HAR Dataset/") 
  
# Reading trainings tables:  
xtrain <- read.table("./data/UCI HAR Dataset/train/X_train.txt")  
ytrain <- read.table("./data/UCI HAR Dataset/train/y_train.txt")  
subjecttrain <- read.table("./data/UCI HAR Dataset/train/subject_train.txt")  

# Reading testing tables:  
xtest <- read.table("./data/UCI HAR Dataset/test/X_test.txt")  
ytest <- read.table("./data/UCI HAR Dataset/test/y_test.txt")  
subjecttest <- read.table("./data/UCI HAR Dataset/test/subject_test.txt")  

# Reading feature vector:  
features <- read.table('./data/UCI HAR Dataset/features.txt')  

# Reading activity labels:  
activityLabels = read.table('./data/UCI HAR Dataset/activity_labels.txt')  

#Merge into one data set  
xData <- rbind(xtrain, xtest)  
yData <- rbind(ytrain, ytest)  
subjectData <- rbind(subjecttrain, subjecttest)  

#demonstrate functionality  
dim(xData)  
dim(yData)  
dim(subjectData)
```
  
# 2. Extract measurments on the mean and std Dev for each measurement  
```R xDataMS <- xData[, grep("-(mean|std)\\(\\)", features[,2])]  
names(xDataMS) <- features[grep("-(mean|std)\\(\\)", features[,2]),2]  

#demonstrate functionality  
View(xDataMS)  
dim(xDataMS)
```
  
# 3. Descriptive activity names  
```R yData[,1] <- activityLabels[yData[,1],2]  
names(yData) <- "Activity"
```  

# 4. Label dataset with variable names  
```R names(subjectData) <- "Subject"  

#demonstrate functionality  
View(yData)  
summary(subjectData)  

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

# 5. Create tidy data set with average of each variable for each activity and each subject  
```R Data2<-aggregate(. ~Subject + Activity, Data, mean)  
Data2<-Data2[order(Data2$Subject,Data2$Activity),]  
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
```
