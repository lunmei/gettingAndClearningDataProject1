---
title: "README"
output: html_document
---
R analysis program. Read the data set and export txt file (tidy_data.txt) including the average of each measurements for each activity and each subject. 

# Read all the datat sets

```r
features <- read.table("features.txt")
activity_labels <- read.table("activity_labels.txt")

X_train <- read.table("./train/X_train.txt")
subject_train <-read.table("./train/subject_train.txt")
y_train <-read.table("./train/y_train.txt")

X_test <- read.table("./test/X_test.txt")
subject_test <-read.table("./test/subject_test.txt")
y_test <-read.table("./test/y_test.txt")
```

# Merge test and train data sets

```r
X_data = merge(X_test, X_train, all=TRUE )
```

# Extract only mean and std value for each measurements

```r
meanList <-grep("mean", features[,2])
X_datamean<-X_data[, meanList]
stdList  <-grep("std", features[,2])
X_datastd <-X_data[,stdList]
X_data_meanstd <- cbind(X_datamean, X_datastd)
```

# Appropriately labels the data set with descriptive variable names.

```r
nameList <-c(as.character(features[meanList,2]), as.character(features[stdList,2]))
names(X_data_meanstd) <- nameList
```

# Uses descriptive activity names to name the activities in the data set

```r
activityList <-c(y_test[,1], y_train[,1])
factor_activityList<- factor(activityList)
levels(factor_activityList) <- activity_labels[,2]
```

# Create tidy data set with mean of each measurements for each subject and each activity

```r
subjectList <-c(subject_test[,1], subject_train[,1])

subActList<-cbind(subjectList, activityList)
X_data_meanstd <- cbind(subActList, X_data_meanstd)

tidy_data <- X_data_meanstd[1:180,]
split1 <- split(X_data_meanstd, X_data_meanstd$subjectList)
l = 1;
for (i in 1:30){
  split2<-split(split1[[i]], split1[[i]]$activityList)
  for (j in 1:6){
    tidy_data[l,]<- colMeans(split2[[j]], na.rm = TRUE)
    l <- l+1
  }
}

temp_factor<- factor(tidy_data$activityList)
levels(temp_factor) <- activity_labels[,2]
tidy_data$activityList <- temp_factor
```

# Write the txt file with tidy data

```r
write.table(tidy_data,file = "tidy_data.txt", row.names = FALSE)
```
