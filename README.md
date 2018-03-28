# GC_data_W4_assign#
# download and unzip data, name the data file as "w4project"#
if(!file.exists("./data")) dir.create("./data")
fileUrl <-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl, destfile = "./data/getcleandataw4project.zip")
w4project <- unzip("./data/getcleandataw4project.zip", exdir = "./data")
w4project

features <- read.csv("./data/UCI HAR Dataset/features.txt", header = FALSE, sep = "")
features <- as.character(features[,2])

X_train <- read.table("./data/UCI HAR Dataset/train/X_train.txt")
Y_train <- read.csv("./data/UCI HAR Dataset/train/y_train.txt", header=FALSE, sep = "")
Subject_train <- read.csv("./data/UCI HAR Dataset/train/subject_train.txt", header=FALSE, sep = "")
train<-data.frame(Subject_train, Y_train, X_train)
names(train)<-c(c("subject", "activity"), features)

X_test <- read.table("./data/UCI HAR Dataset/test/X_test.txt")
Y_test <- read.csv("./data/UCI HAR Dataset/test/y_test.txt", header=FALSE, sep = "")
Subject_test <- read.csv("./data/UCI HAR Dataset/test/subject_test.txt", header=FALSE, sep = "")
test<-data.frame(Subject_test, Y_test, X_test)
names(test)<-c(c("subject", "activity"), features)

#1. Merges the training and the test sets to create one data set.#
Merged <- rbind(train, test)

#2. Extracts only the measurements on the mean and standard deviation for each measurement.#
mean_std.sel<-grep("mean|std", features)
Merged.sub<- Merged[,c(1, 2, mean_std.sel + 2)]

#3. Uses descriptive activity names to name the activities in the data set.#
activity.labels <- read.table('./data/UCI HAR Dataset/activity_labels.txt', header = FALSE)
activity.labels <- as.character(activity.labels[,2])
Merged.sub$activity <- activity.labels[Merged.sub$activity]

#4. Appropriately labels the data set with descriptive variable names.#

names(Merged.sub) <- gsub("[(][)]", "", names(Merged.sub))
names(Merged.sub) <- gsub("^t", "Time", names(Merged.sub))
names(Merged.sub) <- gsub("^f", "Frequency", names(Merged.sub))
names(Merged.sub) <- gsub("Acc", "Accelerometer", names(Merged.sub))
names(Merged.sub) <- gsub("Gyro", "Gyroscope", names(Merged.sub))
names(Merged.sub) <- gsub("Mag", "Magnitude", names(Merged.sub))
names(Merged.sub) <- gsub("-mean-", "Mean", names(Merged.sub))
names(Merged.sub) <- gsub("-std-", "STD", names(Merged.sub))


#5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.#

dim(Merged.sub)
head(Merged.sub,1)
data.tidy <- aggregate(Merged.sub[,3:81], by = list(activity = Merged.sub$activity, subject = Merged.sub$subject),FUN = mean)
write.table(x = data.tidy, file = "GC_data_W4_assign.txt", row.names = FALSE)
