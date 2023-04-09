---
title: "library(strugglR) #1: Subsetting Data Frames with Variable Labels"
show_date: true 
---

Welcome to the first installment of a series of posts I will be hosting on this page, which I like to call library(strugglR) (to my knowledge, there is no existing library with this name readily available on CRAN, so I get dibs). This series will go over methods and tricks I use to work around issues in R/Rmarkdown. I will focus on the obstacles which take more than a simple stack overflow page, the ones that I might very well dump hours into, so you do not have to. Installments of this series can summarize the findings of multiple stack overflow pages or work arounds I have discovered on my own. Feel free to contact me if you have any questions. Let's get into it. 

Recently, in working with the National Crime Vicitmization Survey, I ran into an issue in data cleaning and subsetting. This data, which provides a plethora of information, names the variables based on an ID value whichs is referenced in the 800+ page codebook. This would make analysis exhausting, if it was not for the attached variable desscriptions in each column, which provide descriptions of each column. Subsetting this data however removes these variables. We will illustrate this situation and solution with a simpler example below. 

Feel free to follow along with the provided code. 
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

![Our Initial Data Frame, with descriptions under each Column Name.][(https://github.com/gspiga/gspiga.github.io/blob/master/assets/images/struggleR/image.png?raw=true)]
