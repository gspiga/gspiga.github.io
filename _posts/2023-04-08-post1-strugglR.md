---
title: "library(strugglR) #1: Retaining Variable Labels in Data Subsets"
show_date: true 
---

Welcome to the first installment of a series of posts I will be hosting on this site, which I like to call library(strugglR) (to my knowledge, there is no existing library with this name readily available on CRAN, so I get dibs). This series will go over methods and tricks I use to work around issues in R/Rmarkdown. I will focus on the obstacles which take more than a simple stack overflow page to solve. The ones that I might very well dump hours into, so you do not have to. Installments of this series can summarize the findings of multiple stack overflow pages or work arounds I have discovered on my own. Feel free to contact me if you have any questions. Let's get into it. 

Recently, in working with the 2020 National Crime Vicitmization Survey, I ran into an issue in data cleaning and subsetting. This data, which provides a plethora of information, names the variables based on an ID value whichs is referenced in the 800+ page codebook. This would make analysis exhausting, if it was not for the attached variable desscriptions in each column, which provide descriptions of each column. Subsetting this data however removes these variables. We will illustrate this situation and solution with a simple example below. 

```
# Our sample data frame
df <- data.frame(
  id = c(10,11,12,13,14,15,16,17),
  name = c('sai','ram','deepika','sahithi','kumar','scott','Don','Lin'),
  gender = c('M','M','F','F','M','M','M','F'),
  dob = as.Date(c('1990-10-02','1981-3-24','1987-6-14','1985-8-16',
                  '1995-03-02','1991-6-21','1986-3-24','1990-8-26')),
  state = c('CA','NY',NA,NA,'DC','DW','AZ','PH'),
  "Do they like French Fries?" = c("Y", "Y", "N", NA, 'Y', 'N', 'Y', 'N')
)
attr(df, "variable.labels") = names(df)
names(df) <- c("V100", "V101", "V102", "V103", "V104", "V105")
View(df)
```
Viewing the above data frame, we see the following: 

![Our Initial Data Frame, with descriptions under each Column Name](https://github.com/gspiga/gspiga.github.io/blob/master/assets/images/struggleR/df_full_varlabels.png?raw=true)

However, say we try to subset it, trying to get only the ID, DOB, and whether or not these people like French fries. 
```
df.red <- df[,c(1,3,6)]
```
![Our reduced data frame with no variable labels](https://github.com/gspiga/gspiga.github.io/blob/master/assets/images/struggleR/df_full_varlabels.png?raw=true)

So where did our variable labels go? The answer lies in how these variable names are implemented to the dataframe object. Checking for the attibutes of the dataframe using `attributes(df)` will show the variable labels part of the data frame. However, subsetting this data frame removes that attribute since you are creating a new data frame, with out transferring over all the attributes. Here a documented fix: 

```
# Check the attributes of the original data frame 
attributes(df)
# We grab the variable labels as a chracter vector
labels <- attr(df, "variable.labels")

# Depending on how the data is prepared, the variable labels might already be named.  If they aren't, run this line.
names(labels) <- names(df)

library(Hmisc)
# We know use upData() to make each variable label an attribute of each column instead of just the dataframe
df.new <- upData(df.red, labels = labels.list)
View(df.new)
```
With each variable label an attribute to each column, any further subsetting of the data frame still retains the variable labels, since they are now a unique attribute of each column, and not an overall data frame. We can confirm this by checking the attributes of the data frame and the column. 

```
attributes(df.new)
# $names
# [1] "V100" "V102" "V105"
# 
# $row.names
# [1] 1 2 3 4 5 6 7 8
# 
# $class
# [1] "data.frame"

attributes(df.new$V105)
attributes(df.new)
# $names
# [1] "V100" "V102" "V105"
# 
# $row.names
# [1] 1 2 3 4 5 6 7 8
# 
# $class
# [1] "data.frame"
attributes(df.new$V105)
# $label
# [1] "Do.they.like.French.Fries."
# 
# $class
# [1] "labelled"  "character"
```
