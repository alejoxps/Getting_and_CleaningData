##Code book For Getting_and_CleaningData Project

##Asumed Working Directory as UCI HAR Dataset
#Reading file test
X_train <- read.table("train/X_train.txt")
#Reading file test
X_test <- read.table("test/X_test.txt")#Reading file test
#Reading file features
features <- read.table("features.txt")

##4.Appropriately labels the data set with descriptive activity names.
#labeling X_train data set
colnames(X_train)<-features$V2
#labeling X_test data set
colnames(X_test)<-features$V2

##1.Merges the training and the test sets to create one data set.

X_final<-rbind(X_test,X_train)
##X_final contains X_train and X_test data sets

##2.Extracts only the measurements on the mean and standard deviation for each measurement
#Regular expresion for getting the mean and std vars
X_final<-subset(X_final, select = grepl("mean[(][)]|mean()-X|mean()-Y|mean()-Z|std[(][)]|std()-X|std()-Y|std()-Z", names(X_final)))

##3.Uses descriptive activity names to name the activities in the data set

#Load the library for join
library(plyr)#load library plyr

#Reading the data for activities
y_test <- read.table("test/y_test.txt")
y_train <- read.table("train/y_train.txt")
#Reading Activity labels
activity_labels <- read.table("activity_labels.txt")
#Changing column names
colnames(y_test)<-c("ID")
colnames(y_train)<-c("ID")
colnames(activity_labels)<-c("ID","Activity")
#Joining data sets
A_test<-join(y_test,activity_labels) 
A_train<-join(y_train,activity_labels)
#Merging the data sets
A_final<-rbind(A_test,A_train)
##Uses descriptive activity names to name the activities in the data set APPLY
X_final<-cbind(X_final,A_final$Activity)
#Rename the column added (not necessary to know the exact location)
names(X_final)[names(X_final) == 'A_final$Activity'] <- 'Activity'

#reading Subjects files
subject_test <- read.table("test/subject_test.txt")

subject_train <- read.table("train/subject_train.txt")

#Merging the data sets
subject_final<-rbind(subject_test,subject_train)
colnames(subject_final)<-c("Subject")
X_final<-cbind(X_final,subject_final)

##5.Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 

tidy_data_set<-aggregate(X_final[,1:66], by=list(X_final$Subject,X_final$Activity), 
                                         FUN=mean, na.rm=TRUE)
#Appropiate labeled of final vars
names(tidy_data_set)[1]<-'Subject'
names(tidy_data_set)[2]<-'Activity'
#Sorting the data by subject
tidy_data_set <- tidy_data_set[order(tidy_data_set$Subject),]
#Writting the data
write.table(tidy_data_set, "tidy_data_set.txt",row.names=FALSE)



