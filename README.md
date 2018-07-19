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
