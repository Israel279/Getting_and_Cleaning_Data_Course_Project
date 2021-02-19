# Getting_and_Cleaning_Data_Course_Project
library(data.table)
library(reshape2)

#Activity labels
al <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/activity_labels.txt")[,2]
#Features
f <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/features.txt")[,2]
#Extract mean and standard deviation 
ms <- grepl("mean|std", f)

#####################
#Test Data
#####################

#X_test data
X_test <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/test/X_test.txt")
#Y_test data 
Y_test <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/test/Y_test.txt")
#Subject_test data
ST <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/test/subject_test.txt")
#Use the features as the names of the table X_test
names(X_test) = f
#Extract only the columns with the mean and standard deviation of each measurement 
X_test <- X_test[, ms]
#Asign the Activity labels to de Y_test table using the code
Y_test[,2] <- al[Y_test[,1]]
#Rename columns 
names(Y_test) <- c("Act_ID","Act_Label")
names(ST) <-  "Subject"


#Bind tables using cbind
TestD <- cbind(as.data.table(ST), Y_test, X_test)

#####################
#Train Data
#####################

#X_train data
X_train <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/train/X_train.txt")
#Y_train data
Y_train <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/train/Y_train.txt")
#Subject_train data 
S_train <- read.table("E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/UCI HAR Dataset/train/subject_train.txt")
#Use the features as the names of the table X_train
names(X_train) = f
#Extract only the columns with the mean and standard deviation of each meadurement
X_train <- X_train[, ms]
#Asign the Activity labels to de Y_train table using the code
Y_train[,2] <- al[Y_train[,1]]
#Rename columns 
names(Y_train) <- c("Act_ID", "Act_Label")
names(S_train) <- "Subject"

#Bind tables using cbind
TrainD <- cbind(as.data.table(S_train), Y_train, X_train)


#############################
#Merge Test & Train Data
#############################

D <- rbind(TestD, TrainD)

#Select only the labels that aren't in c()
Data_labels <- setdiff(colnames(D), c("Subject", "Act_ID", "Act_Label"))
#use Data labels now as a cathegorical variable 
M_Data <- melt(D, id = c("Subject", "Act_ID", "Act_Label"), measure.vars = Data_labels)

#############################
#From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
#############################
 
TD <- dcast(M_Data, Subject + Act_Label ~ variable, mean)

write.table(TD, file = "E:/CURSO R JOHNS HOPKINS UNIVERSITY/2 Getting and Cleaning Data/Proyecto Final/TD.txt")
