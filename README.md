# Course-project---getting-and-cleaning-data
#The first thing I did was to download the files from this location:

# https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

# After unzipping the downloaded file it is very important to set the folder UCI HAR Dataset as the working directory in R.  #Download the script and place it in the UCI HAR Dataset folder. To run the script typ source("run_analysis.R") in the console. #To see the final output of the the average of each variable for each activity and each subject - typ upload in the console. 


# If not, the script will not work. Below is my code with explanatory comments on each row to make the steps easier to follow:

x_test <- read.table("./test/X_test.txt")  		# loading the X_test file into R
y_test <- read.table("./test/y_test.txt") 		# loading the y_test file into R
x_train <- read.table("./train/X_train.txt") 		# loading the X_train file into R
y_train <- read.table("./train/y_train.txt") 		# loading the y_train file into R
subject_test <- read.table("./test/subject_test.txt") 	# loading the subject_test file into R
subject_train <- read.table("./train/subject_train.txt") 	# loading the subject_train file into R
features <- read.table("features.txt")			# loading the features file into R
column_names <- features[, 2]				# save the second column of the features file to later be used as column names
rbind_X <- rbind(x_test, x_train)				# combine the x_test and the x_train file into one file
rbind_y <- rbind(y_test, y_train)                                                # combine the y_test and the y_train file into one file
rbind_participants <- rbind(subject_test, subject_train)	# combine the two different list of  participants 
colnames(rbind_X) <- column_names			# naming the columns with appropriate names from the features file
colnames(rbind_y)[1] <- "activity"				# naming the activity column 
colnames(rbind_participants)[1] <- "subject"			# naming the subject column
Masterdata <- cbind(rbind_participants, rbind_y, rbind_X) 	# Combining all the data into one dataset
Masterdata_no_duplicate_colnames <- Masterdata[ , !duplicated(colnames(Masterdata))]	# removing columns with no unique column names. mean and std columns not affected  
Extract_subject <-  select(Masterdata_no_duplicate_colnames, contains("subject")) 	# extracting the subject column
Extract_activity <-  select(Masterdata_no_duplicate_colnames, contains("activity")) 	# extracting the subject column
Extract_mean <-  select(Masterdata_no_duplicate_colnames, contains("mean")) 	# extracting columns with the string “mean”
Extract_std <-  select(Masterdata_no_duplicate_colnames, contains("std"))		# extracting columns with the string “std”
Extract_subject_activity_mean_std <- cbind(Extract_subject, Extract_activity, Extract_mean, Extract_std) 	# combining the extracted columns into a new table
activity_labels <- read.table("activity_labels.txt")			# loading the activity_labels file to be able change integer to actual activity name 
colnames(activity_labels) <- "activity"				# naming the column activity	
final_dataframe <- merge(activity_labels, Extract_subject_activity_mean_std, by.x = "activity", by.y = "activity", all = TRUE)	# merging activity labels with extracted columns 
final_dataframe <- final_dataframe[ ,2:89]				# removing the first unnecessary column of integers 
colnames(final_dataframe)[1] <- "activity"				# name the first column “activity”
final_dataframe <- arrange(final_dataframe, subject, activity)	# arrange the column
combined_id_variables <- paste(final_dataframe$subject, final_dataframec$activity)	# create a new a new unique id variable for each activity and subject
upload <- cbind(combined_id_variables, final_dataframe)	# combining the new id variable with the final_dataframe in a new data frame 
upload <- melt(upload, id=c("combined_id_variables", "subject", "activity"))	# melting the new data frame
upload <- dcast(upload, combined_id_variables ~ variable, mean) # using the new id variable to calculate the average of each variable for each activity and each subject
