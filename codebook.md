CodeBook



download the zipfile  metioned in the readme.md, from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

unzip the data and put them into your working directory.


Read the Activity Files

```r
dataActivityTest <- read.table("~/UCI HAR Dataset/test/y_test.txt", head = F)
dataActivityTrain <- read.table("~/UCI HAR Dataset/train/y_train.txt", head = F)
```

Read the Subject Files

```r
dataSubjectTest <- read.table("~/UCI HAR Dataset/test/subject_test.txt", head = F)
dataSubjectTrain <- read.table("~/UCI HAR Dataset/train/subject_train.txt", head = F)
```


Read the Fearures Files

```r
dataFeaturesTest <- read.table("~/UCI HAR Dataset/test/X_test.txt", head = F)
dataFeaturesTrain <- read.table("~/UCI HAR Dataset/train/X_train.txt", head = F)
```



Read Fearures Name File

```r
dataFeaturesName <- read.table("~/UCI HAR Dataset/features.txt", head = F)
```



Merges the training and the test sets to create one data set
Concatenate the data tables by rows

```r
dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity <- rbind(dataActivityTrain, dataActivityTest)
dataFeatures <- rbind(dataFeaturesTrain, dataFeaturesTest)
```


Set names to Variables

```r
names(dataSubject) <- c('Subject')
names(dataActivity) <- c('Activity')
names(dataFeatures) <- dataFeaturesName$V2
```



Mergre columns to get the data frame for all data

```r
dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)
```



Extracts only the measurements on the mean and standard deviation for each measurement
Subset Name of Features by measurements on the mean and standard deviation

```r
subdataFeaturesName <- dataFeaturesName$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesName$V2)]
```



Subset the data frame by selected name of Features

```r
selectedName <- c(as.character(subdataFeaturesName),"Subject","Activity")
Data <- subset(Data, select = selectedName)
```



Uses descriptive activity names to name the activities in the data set
Read descriptive activity names from "activity_lables.txt"

```r
activityLables <- read.table("~/UCI HAR Dataset/activity_labels.txt",head=F)
```


Facotrize Activity in the data frame

```r
Data$Activity <-factor(Data$Activity)
Data$Activity <-factor(Data$Activity,labels=as.character(activityLables$V2))
```



Appropriately labels the data set with descriptive variable names
In the former part, variables activity and subject and names of the activities have been labelled using descriptive names.In this part, Names of Feteatures will labelled using descriptive variable names.
1.prefix t is replaced by time
2.Acc is replaced by Accelerometer
3.Gyro is replaced by Gyroscope
4.prefix f is replaced by frequency
5.Mag is replaced by Magnitude
6.BodyBody is replaced by Body

```r
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))
```



Creates a second, independent tidy data set and output it

```r
library(plyr)
Data2 <-aggregate(.~Subject + Activity, Data, mean)
Data2 <-Data2[order(Data2$Subject,Data2$Activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
```



