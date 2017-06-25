##Read the Activity Files
dataActivityTest <- read.table("~/UCI HAR Dataset/test/y_test.txt", head = F)
dataActivityTrain <- read.table("~/UCI HAR Dataset/train/y_train.txt", head = F)

##Read the Subject Files
dataSubjectTest <- read.table("~/UCI HAR Dataset/test/subject_test.txt", head = F)
dataSubjectTrain <- read.table("~/UCI HAR Dataset/train/subject_train.txt", head = F)

##Read the Fearures Files
dataFeaturesTest <- read.table("~/UCI HAR Dataset/test/X_test.txt", head = F)
dataFeaturesTrain <- read.table("~/UCI HAR Dataset/train/X_train.txt", head = F)

##Read Fearures Name File
dataFeaturesName <- read.table("~/UCI HAR Dataset/features.txt", head = F)

## Merges the training and the test sets to create one data set
## Concatenate the data tables by rows
dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity <- rbind(dataActivityTrain, dataActivityTest)
dataFeatures <- rbind(dataFeaturesTrain, dataFeaturesTest)

## Set names to Variables
names(dataSubject) <- c('Subject')
names(dataActivity) <- c('Activity')
names(dataFeatures) <- dataFeaturesName$V2

## Mergre columns to get the data frame for all data
dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)

## Extracts only the measurements on the mean and standard deviation for each measurement
## Subset Name of Features by measurements on the mean and standard deviation
subdataFeaturesName <- dataFeaturesName$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesName$V2)]

## Subset the data frame by selected name of Features
selectedName <- c(as.character(subdataFeaturesName),"Subject","Activity")
Data <- subset(Data, select = selectedName)

## Uses descriptive activity names to name the activities in the data set
## Read descriptive activity names from "activity_lables.txt"
activityLables <- read.table("~/UCI HAR Dataset/activity_labels.txt",head=F)
## Facotrize Activity in the data frame
Data$Activity <-factor(Data$Activity)
Data$Activity <-factor(Data$Activity,labels=as.character(activityLables$V2))

## Appropriately labels the data set with descriptive variable names
##In the former part, variables activity and subject and names of the activities have been labelled using descriptive names.In this part, Names of Feteatures will labelled using descriptive variable names.
##prefix t is replaced by time
##Acc is replaced by Accelerometer
##Gyro is replaced by Gyroscope
##prefix f is replaced by frequency
##Mag is replaced by Magnitude
##BodyBody is replaced by Body

names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))

##Creates a second, independent tidy data set and output it
library(plyr)
Data2 <-aggregate(.~Subject + Activity, Data, mean)
Data2 <-Data2[order(Data2$Subject,Data2$Activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)

