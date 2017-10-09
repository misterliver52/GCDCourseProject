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
# Data Source and Definitions  

Human Activity Recognition Using Smartphones DatasetVersion 1.0  
Jorge L. Reyes-Ortiz, Davide Anguita, Alessandro Ghio, Luca Oneto.Smartlab - Non Linear Complex Systems LaboratoryDITEN - Università degli Studi di Genova.Via Opera Pia 11A, I-16145, Genoa, Italy.  
activityrecognition@smartlab.ws  
www.smartlab.ws  
  
> The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.  

> The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. See 'features_info.txt' for more details. 

> For each record it is provided:  

>- Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration.- Triaxial Angular velocity from the gyroscope. - A 561-feature vector with time and frequency domain variables. - Its activity label. - An identifier of the subject who carried out the experiment.  
  
> The dataset includes the following files:  

>- 'README.txt'  
>- 'features_info.txt': Shows information about the variables used on the feature vector.  
>- 'features.txt': List of all features.  
>- 'activity_labels.txt': Links the class labels with their activity name.  
>- 'train/X_train.txt': Training set.  
>- 'train/y_train.txt': Training labels.  
>- 'test/X_test.txt': Test set.  
>- 'test/y_test.txt': Test labels.    
  
> The following files are available for the train and test data. Their descriptions are equivalent.    
>- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30.  
>- 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis.  
>- 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration.  
>- 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second.  
  
> Notes:  
> ======  
>- Features are normalized and bounded within [-1,1].  
>- Each feature vector is a row on the text file.  
  
> For more information about this dataset contact: activityrecognition@smartlab.ws  
 
--<cite>Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. Human Activity Recognition on Smartphones using a Multiclass Hardware-Friendly Support Vector Machine. International Workshop of Ambient Assisted Living (IWAAL 2012). Vitoria-Gasteiz, Spain. Dec 2012</cite>  
  
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
