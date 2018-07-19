# Data & Code Description 
As per assignment given in the course "Getting and Cleaning Data", the smartwatches data has been provided.
There are two folders in the data set:
1. test
2. train

Each folder contains three different files of data:
1. subject_X, subject_train : Containing the specific numeral fot subect identification (There are 30 subjects in total.Some are for test, rest are for train)
2. X_test,X_train : Containing observations of 561 variables as described in the "features.txt"
3. y_test,y_train : Containing the activity labels for each observation

Code "run_analysis.R" does the following steps:
1. Reads the activity labels and names of 561 variables.
2. Crawls in the test and train folders to read the containing files accordingly
3. Reads and stores the "test" folder files and stores them in the respective data frame.
4. Similarly stores the "train" folder files.
5. Adds a column "Test_or_Train" in "X_test" and "X_train" datasets
6. Merges all the "test" files by cbind (Subject_test, X_test,y_test) 
7. Similarly merges all the "train" files
8. Finally mergers "test"  from point-6 and "train" from point-7by rbind
9. Removes the variables other than mean and Std dev form the 561 variable set in the dta feam from point-8
10. Creates a new dataframe "summarized_data" which groups the data by subject and activity and displays the average value for each column for each group.

# CODE 
#Problem statements of the assignment
#1: Merging trainig and test data sets
#2: Extract only mean and std dev 
#3: Use descritive activity name
#4: Label with variable names
#5: Create a second independent data with the average of each variable for each activity and subject

library(plyr)
library(dplyr)

#for time calculation
start_time <- Sys.time()

# -------------------SOLUTION TO PROBLEM STATEMENT 1,2,3 & 4------------------------#

working_dir <- "E:/000DATA_Science_Coursera2018/03_Getting_&_Cleaning_Data/Assignment/UCI HAR Dataset"
setwd("E:/000DATA_Science_Coursera2018/03_Getting_&_Cleaning_Data/Assignment/UCI HAR Dataset")
files <- list.files()

#Activity Labels
activity_labels_df <- read.table("activity_labels.txt")
activity_labels <- as.character(activity_labels_df$V2)


#561_variables_names
features_df <- read.table("features.txt")
features <- as.character(features_df$V2)

test_folder <- files[5]
train_folder <- files[6]

test_folder_files <- list.files(test_folder)
train_folder_files <- list.files(train_folder)

#Test Folder files path
subject_test_path <- paste(test_folder,"subject_test.txt", sep = "/")
X_test_path <- paste(test_folder,"X_test.txt", sep = "/")
y_test_path <- paste(test_folder,"y_test.txt", sep = "/")

#Train Folder files path
subject_train_path <- paste(train_folder,"subject_train.txt", sep = "/")
X_train_path <- paste(train_folder,"X_train.txt", sep = "/")
y_train_path <- paste(train_folder,"y_train.txt", sep = "/")

#Test Files access
subject_test <- read.table(subject_test_path)
X_test <- read.table(X_test_path)
new_col_test <- c()
n_test <- nrow(X_test)
for (i in 1:n_test){
  new_col_test <- c("test",new_col_test )
}
X_test<- cbind(new_col_test,X_test)
y_test <- read.table(y_test_path)

#Train files access
subject_train <- read.table(subject_train_path)
X_train <- read.table(X_train_path)
new_col_train <- c()
n_train <- nrow(X_train)
for (j in 1:n_train){
  new_col_train <- c("train",new_col_train)
}
X_train<-cbind(new_col_train, X_train)
y_train <- read.table(y_train_path)


#Colnames for combined_files
#col_names_vector <- c("Subject","Activity",features)
 col_names_vector <- c("Subject","Activity","Test_or_Train",features)

#Combined_test_files
combined_test <- cbind(subject_test,y_test,X_test)
colnames(combined_test)<- col_names_vector
combined_test$Activity <- mapvalues(combined_test$Activity, from = c(1:6), to = activity_labels)


#Combined_train_files
combined_train <- cbind(subject_train,y_train,X_train)
colnames(combined_train)<- col_names_vector
combined_train$Activity <- mapvalues(combined_train$Activity, from = c(1:6), to = activity_labels)


#Selecting only the mean and standard deviations variables
first_num <- 3
my_seq <- seq(first_num,564,40)
my_seq
req_cols <- c(1,2,3)

for(i in my_seq){
  for (j in 1:5){
    k <- i+j
  req_cols <-c(req_cols,k)
  }
}
req_cols <- as.vector(req_cols)
all_cols <- c(1:564)
not_req_cols <- as.vector(all_cols[-req_cols])

combined_test <- combined_test[-not_req_cols]
combined_train <- combined_train[-not_req_cols]

summary_sub_test<- table(subject_test)
summary_sub_train <- table(subject_train)

all_test_train <- rbind(combined_test,combined_train)

#Writing the answer file
ans_path <- "E:/000DATA_Science_Coursera2018/03_Getting_&_Cleaning_Data/Assignment/Processed_files"
write.table(all_test_train, file = paste(ans_path,"all_test_train.txt",sep = "/"), row.names = FALSE, col.names = TRUE)

#------------------PROBLEM STATEMENT 1,2,3 & 4 FINISHED ---------------------#

# -------------------SOLUTION TO PROBLEM STATEMENT 5------------------------#

#Making independent data set to get the average of each variable for... 
#each activity and each subject

summarized_data <- all_test_train %>% group_by(Test_or_Train, Subject, Activity) %>% summarise_all(funs(mean))

#writing the answer file
write.table(summarized_data, file = paste(ans_path,"summarized_data.txt",sep = "/"), row.names = FALSE, col.names = TRUE)

#Time Calculation
end_time <- Sys.time()
running_time <- end_time - start_time
running_time
#------------------PROBLEM STATEMENT 1,2,3 & 4 FINISHED ---------------------#


# CodeBook_Details :

# Data:
Data used for analysis are:
1.	features.txt
2.	activity_labels
3.	Data provided in test & train folder:
a.	subject_test, subject_train : Subjects for test and train data
b.	X_test, X_train : The measured data of 561-variables (provided in features.txt) for subjects of test and train respectively
c.	y_test, y_train : Activity labels in numerical format for each observation for test and train

# Variables:
Appropriately labeled the activities. Appropriately labeled the columns
•	For “all_test_train”
1.	Subject
2.	Activity ( descriptive) 
3.	Test_or_Train
4.	Mean & Std dev variables
•	For “summarized_data”
1.	Subject
2.	Activity (descriptive)
3.	Test_or_Train
4.	Average value of other variables in their respective column

# Transformations:
As we are combining test & train data, I have added one column (“Test_or_Train”)  in each “X_test” and “X_train” data just to notify observations of test or train respectively.

