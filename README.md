---
title: "Week Two"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Homework: Loading a data set into R.  What questions do you all have?

Brief review

Creating data frames and extracting data from the data frames.  Data frames put the variables into a form that we are used to where we have the variable names on the top row and "people" or data points for each variable below each variable.  Remember that when we are creating variables and we want to include multiple data points, we need to use c() with commas to seperate them.  We need to use the data.frame to turn a data set into a data frame.

When we have a data frame, we can access the variables inside the data frame using the $ or [].
```{r}
datReview = data.frame(a = c(1,2,3), b = c(4,5,6))
datReview[,1]
datReview$a
```

Just run this code.  What it is doing is creating a data set with three variables.  Two continous variables and one ordinal variable and creating some NA values, so I can show you how to deal with NAs. 

We can pretend that we have two outcomes variables, a 5-point Likert satisfaction score, and a gender variable with two options male and female.
```{r}
ordVar = c(1,2,3,4,5)
binVar = c(0,1)
set.seed(123)
datWeekTwo = data.frame(outcome1 = rnorm(100), outcome2 = rnorm(100), satisfaction = sample(ordVar, 100, replace = TRUE), gender = sample(binVar, 100, replace = TRUE))
datWeekTwo[1:10,1] = NA
datWeekTwo[11:15,2] = -99
datWeekTwo
```
The first thing you want to do is set the working directory.  This tells R where you want to read in and store data sets.  Go to the session, set working directory, then choose the working directory.  Then you can copy that path into the code so you don't have to do that every time.

***** I am working on a mac so make sure you don't copy and paste the setwd directly from this page and you actually find the specific file path for your computer if you have a PC.

Let's first export the data set that we have to a csv file because that is the easiest file to work with.  We can use the write.csv function to do that.  Row names are likely to be false.

Then you can read the csv file using the read.csv function.  Most of the time the first row in the dataset will be the variable names, so you will need to set the header to be true.  


Sometimes you have multiple values that signal a value is NA (e.g. -99, -77).  To load all the possible NA values into R and turn them all into NA's use the na.strings function and concatinate the list of values using c() and put each character (i.e. non-integer) in quotation marks and each value should be separated by a comma.  

For data that is left blank you can use the " " with one space.  Matt provide example during workshop.

If you want to get rid of missing values you can use the na.omit function.  This function deletes any row that has at least one missing value for at least one variable. 
```{r}
setwd("C:/Users/Matthew.Hanauer/Desktop")
write.csv(datWeekTwo, "datWeekTwo.csv", row.names = FALSE)
datWeekTwo = read.csv("datWeekTwo.csv", header = TRUE, na.strings = c("NA",-99, " "))
datWeekTwo
datWeekTwo = na.omit(datWeekTwo)
datWeekTwo
```
To get some summary statistics we will need some different statistical packages.  This means we need to use the install.packages function to install the psych and prettyR packages and then library them.  You only need to install packages once (unless you get an updated version of R), but you need to library the package every time you restart R.  

You can also get summary statistics fairly quickly using summary and or describe for continuous variables and describe.factor for ordinal, categorical, and binary types.

describe.factor only works with a single variable; however, we will learn how to use it to provide counts and percentages for several variables at a time.
```{r}
#install.packages("psych")
#install.packages("prettyR")
#install.packages("descr")
library(descr)
library(psych)
library(prettyR)
summary(datWeekTwo)
describe(datWeekTwo)
describe.factor(datWeekTwo$ordVar)
```
Also, to better understand the packages you can use the help function
```{r}
help("summary")
```
Sometimes you want to get cross tabs of different variables.  We can look at the means for males and females using the compmeans function and then round the results.

Also, the round function follows some different rules: https://stat.ethz.ch/R-manual/R-devel/library/base/html/Round.html
```{r}
round(compmeans(datWeekTwo$outcome1, datWeekTwo$gender),2)
```
Sometimes we want to subset the data.  For example, in the satisfaction variable, we can imagine it is on the following scale: 5 = strongly agree, 4 = agree, 3 = neutral, 2 = disagree, 1 = strongly disagree.  We may not be sure what to do with the neutral category so we may want to exclude those people.  We can use the subset function in R.  To subset the data where we exclude neutrals (i.e. 3's), we need two arguments, first is the dataset that we want to subset and second is the condition.  In this example, we want to exclude 3's so we say satisfaction!=3 to exclude the 3's.  In the other example below I show how to subset where you only include 5's using the == operator.  
```{r}
datWeekTwo = subset(datWeekTwo, satisfaction != 3)
datWeekTwo$satisfaction
datWeekTwoExample = subset(datWeekTwo, satisfaction == 5)
datWeekTwoExample$satisfaction
```
Just like in excel sometimes we want to use an if else statement.  If else statements allow us to change data based on some rules.  For example, in our data set we may want to create a binary variable from the satisfaction variable where we have all agree (strongly agree and agree) as 1 and all disagrees (strongly disagree and disagree) as zero.  We can use an ifelse statement to change the satisfaction variable.  
```{r}
datWeekTwo$satisfaction = ifelse(datWeekTwo$satisfaction >=4, 1, 0)
datWeekTwo$satisfaction
```
Week two homework.  Get means for continuous variables and counts and percentages for at least one binary or ordinal variable from the data you loaded into R.
