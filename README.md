GettingAndCleaningData_project
Reading in dataset for train and test group
the code read in the dataset for train and test groups. Both train and test dataset have three text files to read in, namely x,y and subject. x files records the data measured from the accelerometers, y file records the type of activity where the measurement is taking and the subject file records the ID for the subject where the measurement is taken.

test_y<-read.table("./UCI HAR Dataset/test/y_test.txt")
test_x<-read.table("./UCI HAR Dataset/test/X_test.txt")
test_subject<-read.table("./UCI HAR Dataset/test/subject_test.txt")

train_y<-read.table("./UCI HAR Dataset/train/y_train.txt")
train_x<-read.table("./UCI HAR Dataset/train/X_train.txt")
train_subject<-read.table("./UCI HAR Dataset/train/subject_train.txt")
Merging datasets for train and test groups
Since each row of x, y and subject represents one observation, we can simply column bind these three data set to form one table.

train<-cbind(train_subject,train_y,train_x)
test<-cbind(test_subject,test_y,test_x)
Merging train and test groups data into one dataset
train and test are originally one full data set that was splited into train and test group; the former was used to train the algorithm while the later was used to test the functionality of the algorithm. Hence, train and test are not variables that affect the measurement of the accelerometers and can be merged simply by using row bind.

data<-rbind(train,test)
Renaming the variables with descriptive names
The first column of the data is readed from the subject text file, thus naming subject; the second column is from the y text files, thus renaming to activity. As for the variable names for the accelerometers, the names was read in from the features.txt file.

features<-read.table("./UCI HAR Dataset/features.txt")
names(data)=c("subject","activity",features[[2]])
Extracting meansurement on the mean and std for each measurement
This code extracts the column that recorded the mean and std for each observation.

data_extracted<-select(data,subject,activity,contains(c("mean()","std()")))
Renaming activity with descriptive activity names
This code renames the avtivity identifier from number to descriptive name recored in activity_labels.txt file.

activity_labels<-read.table("./UCI HAR Dataset/activity_labels.txt")
data_extracted$activity=as.factor(data_extracted$activity)
levels(data_extracted$activity) = activity_labels[[2]]
Summarising dataset
This code summarises the whole data set into a table with average of mean and std of every acccecelometers measurements for each subject when performing each activity. The code also write the reuslt to a txt file named step5.txt

data_average<-data_extracted%>%
                group_by(subject,activity,)%>%
                summarise_all(mean,na.rm=TRUE)
write.table(data_average,"step5.txt")
