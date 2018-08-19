# run_analysis.R
New Project based for Getting and Cleaning Data


# Get the Data from the Url by downloading the File and Unzipping
UCIDataSetUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(UCIDataSetUrl, destfile = "F:./UCI HAR Dataset.zip")

outUCIDataSetfile <-"F:./"
unzip("F:./UCI HAR Dataset.zip", exdir=outUCIDataSetfile)
path_rf <- file.path("F:./" , "UCI HAR Dataset")

## Read Data
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
X_test <- read.table("UCI HAR Dataset/test/X_test.txt")
X_train <- read.table("UCI HAR Dataset/train/X_train.txt")
y_test <- read.table("UCI HAR Dataset/test/y_test.txt")
y_train <- read.table("UCI HAR Dataset/train/y_train.txt")

activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt")
features <- read.table("UCI HAR Dataset/features.txt") 

# 1. Merges the training and the test sets to create one data set.
dataSet <- rbind(X_train,X_test)

# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
MeanStdMeasurement <- grep("mean()|std()", features[, 2]) 
dataSet <- dataSet[,MeanStdMeasurement]

# 3. Uses descriptive activity names to name the activities in the data set
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),header = FALSE)
activity_group <- factor(dataSet$activity)
levels(act_group) <- activity_labels[,2]
dataSet$activity <- activity_group

# 4. Appropriately labels the data set with descriptive variable names.
names(dataSet)<-gsub("^t", "time", names(dataSet))
names(dataSet)<-gsub("^f", "frequency", names(dataSet))
names(dataSet)<-gsub("Acc", "Accelerometer", names(dataSet))
names(dataSet)<-gsub("Gyro", "Gyroscope", names(dataSet))
names(dataSet)<-gsub("Mag", "Magnitude", names(dataSet))
names(dataSet)<-gsub("BodyBody", "Body", names(dataSet))

# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(plyr);
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)

