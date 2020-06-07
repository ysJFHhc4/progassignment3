# programming  assignment 3

library(dplyr) ##required for running this package
activities<-read.table("UCI HAR Dataset/activity_labels.txt",col.names = c("code", "activity"))
features<-read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))
ytrain<-read.table("UCI HAR Dataset/train/y_train.txt",col.names = "codes")
xtrain<-read.table("UCI HAR Dataset/train/X_train.txt",col.names = features$functions)
subjecttrain<-read.table("UCI HAR Dataset/train/subject_train.txt",col.names = "subject")
ytest<-read.table("UCI HAR Dataset/test/y_test.txt",col.names = "codes")
xtest<-read.table("UCI HAR Dataset/test/X_test.txt",col.names = features$functions)
subjecttest<-read.table("UCI HAR Dataset/test/subject_test.txt",col.names = "subject")

## The above loads all the data into R

x<-rbind(xtrain,xtest)
y<-rbind(ytrain,ytest)
subject<-rbind(subjecttrain,subjecttest)
mergedata<-cbind(subject,y,x)

## The above merges the data into one dataset

cleandata<-mergedata %>% select(subject,codes,contains("mean"),contains("std"))

## This will create a new dataset of means and standard deviations

names(cleandata)<-gsub("Acc","Accelerometer",names(cleandata))
names(cleandata)<-gsub("Gyro","Gyroscope",names(cleandata))
names(cleandata)<-gsub("BodyBody","Body",names(cleandata))
names(cleandata)<-gsub("Mag","Magnitude",names(cleandata))
names(cleandata)<-gsub("^t","Time",names(cleandata))
names(cleandata)<-gsub("^f","Frequency",names(cleandata))
names(cleandata)<-gsub("tBody","TimeBody",names(cleandata))
names(cleandata)[2]="activity"

## These commands will rename columns to better describe data columns 

cleandata2<-cleandata %>%
        group_by(subject, activity) %>%
        summarise_all(list(mean))
write.table(cleandata2,"cleandata2.txt",row.name=FALSE)

## This command will create a text file with the new dataset 
