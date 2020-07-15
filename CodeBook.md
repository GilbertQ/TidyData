These are the step by step results of the scripts included in the run_analysis.R file:

**0. Loading the library**  
`library(dplyr)`  

**1. Downloading the dataset**  
`filename <- "dataset.zip"`     
`if (!file.exists(filename)){`     
       `fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"`
 `download.file(fileURL,filename)`      
     `}`  
     The dataset is downloaded from the provided link and saved in the dataset.zip file.  
    `if (!dir.exists("UCI HAR Dataset")){`  
    `unzip(filename)`  
     `}`  
     This file is unzipped into the folder UCI HAR Dataset  
     If the file or the folder already exists they are not created.  


**2. The information is loaded into table variables:**  
     `features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))`  
     	This information comes from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ (561 obs. of  2 variables).  
     `activities <-read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))`  
    	This is the list of activities and codes recorded as the experiments were ran (6 obs. of 2 variables)  
     `subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")`  
    	This is the information of the train data -70% of the volunteers- (7352 obs. of 1 variable)  
     `x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)`  
    	This is the information of the data collected for the train part (7352 obs. of 561 variables)  
     `y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")`  
    	This is the information of the labels for the codes of the train activities (7352 obs. of 1 variable)  
     `subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")`  
    	This is the information of the test data -30% of the volunteers- (2947 obs. of 1 variable)  
    `x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)`   
    	This is the information of the data collected for the test part (2947 obs. of 561 variables)  
    `y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")`   
    	This is the information of the labels for the codes of the test activities (2947 obs. of 1 variable)  
       
 

**3. Merges the training and the test sets to create one data set.**  
     `x <- rbind(x_train, x_test)`  
    	Both data from x train and test parts are merged into a new variable (10299 obs. of 561 variables)  
     `y <- rbind(y_train, y_test)`  
    	Both data from y train and test parts are merged into a new variable (10299 obs. of 1 variable)  
     `subject <- rbind(subject_train, subject_test)`  
    	Both subjects from train and test parts are merged into a new variable (10299 obs. of 1 variable)  
     `merged_Data <- cbind(subject, y, x)`  
    	The three variables are merged into a new dataset (10299 obs. of 563 variables)  

**4. Extracts only the measurements on the mean and standard deviation for each measurement:**  
     `neat_Data <- merged_Data %>% select(subject, code, contains("mean"), contains("std"))`  
    	Selecting only the mean and the std subjects the new data is filtered (10299 obs. of 88 variables)  

**5. Uses descriptive activity names to name the activities in the data set**  
     `neat_Data$code <- activities[neat_Data$code,2]`  
    	The code column is replaced with the value from the second column in activities table.  

**6. Appropriately labels the data set with descriptive variable names.**  
     `names(neat_Data)[2] = "Activity"`  
	    	The second column title is changed to Activity  
     `names(neat_Data)<-gsub("Acc", "Accelerometer", names(neat_Data))`  
	    	The Acc word is replaced with Accelerometer in all of the column titles  
     `names(neat_Data)<-gsub("Gyro", "Gyroscope", names(neat_Data))`  
	    	The Gyro word is replaced with Gyroscope in all of the column titles  
     `names(neat_Data)<-gsub("BodyBody", "Body", names(neat_Data))`  
	    	The BodyBody word is replaced with Body in all of the column titles  
     `names(neat_Data)<-gsub("Mag", "Magnitude", names(neat_Data))`  
	    	The Mag word is replaced with Magnitude in all of the column titles  
     `names(neat_Data)<-gsub("^t", "Time", names(neat_Data))`  
	    	The words starting with are replaced with Frecuency in all of the column titles  
     `names(neat_Data)<-gsub("^f", "Frequency", names(neat_Data))`  
	    	The words starting with f are replaced with Frecuency inall of the column titles  
     `names(neat_Data)<-gsub("tBody", "TimeBody", names(neat_Data))`  
	    	The tBody word is replaced with TimeBody in all of the column titles  
     `names(neat_Data)<-gsub("-mean()", "Mean", names(neat_Data), ignore.case = TRUE)`  
	    	The -mean() word is replaced with Mean in all of the column titles in any casing  
     `names(neat_Data)<-gsub("-std()", "STD", names(neat_Data), ignore.case = TRUE)`  
	    	The -std() word is replaced with STD in all of the column titles in any casing  
     `names(neat_Data)<-gsub("-freq()", "Frequency", names(neat_Data), ignore.case = TRUE)`  
	    	The -freq() word is replaced with Frequency in all of the column titles in any casing  
     `names(neat_Data)<-gsub("angle", "Angle", names(neat_Data))`  
	    	The angle word is replaced with Angle in all of the column titles  
     `names(neat_Data)<-gsub("gravity", "Gravity", names(neat_Data))`  
	    	The gravity word is replaced with Gravity in all of the column titles  

**7. From the data set in previous step, creates a second, independent tidy data set with the average of each variable for each activity and each subject.**    
     `TidyData <- neat_Data %>%`  
        `group_by(subject, Activity) %>%`  
        `summarise_all(funs(mean))`  
    	Summarizing the mean of each activity and subject the TidyData table is created (180 rows x 88 columns)  
     `write.table(TidyData, "TidyData.txt", row.name=FALSE)`  
    	Writing the table to a new text file withouth the name of the rows.   
