---
title: "Data wrangling in R"
author: "Emma James"
date: "2024-10-30"
output: html_document
---

In this section of the workshop, we will learn how to manipulate your data in R. Being able to process and reformat your data in a fast and reproducible way is a major benefit of working in R relative to e.g., Excel and SPSS. We will use a package of functions dedicated to this purpose, known as the *tidyverse*. 

If you're following this material directly after the Navigating R content, you can skip the setting up section.

# Setting up

## Load packages

We need to load a collection of functions from the tidyverse. 


``` r
library(tidyverse)
```

## Load data

We will be working with data collected from a study of sleep and word learning in children ([James, Gaskell, & Henderson, 2020](https://acamh.onlinelibrary.wiley.com/doi/full/10.1111/jcpp.13253). The experiment involved teaching children the names of unusual plants/animals, and testing their memory for them after periods of wake and sleep. For this lesson, we will analyse the data from the picture naming task, in which children were asked to name pictures of the items as quickly as they could. We will focus on data from the first two test sessions only (12-hour period containing wake or sleep). 

The folder circulated included folder with the data in it, so we need to instruct R to navigate to the correct file. If you look in the files pane, you can see that we are currently located in the scripts folder. To navigate to the data folder, we need to include "../" in the file path to instruct R to go "up one level", then "data/" to go into the data folder, then the file name (including path extension).]

Let's read in the picture naming file, and assign it to the object "raw_dat".


``` r
raw_dat <- read_csv("./data/picName_raw.csv")
```

```
## Rows: 6602 Columns: 9
## ── Column specification ──────────────────────────────────────────────────────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): ID, group, task, learnTime, item
## dbl (4): week, time, acc, RT
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

You should now see an object named 'raw_dat' in the environment pane, on the top right of your RStudio window. You can see that it has 6602 observations (rows) for 9 variables (columns). You can inspect this by click on the little grid icon next to it. 

We can also inspect the data format by calling `summary()` preview the data using the `head()` function to print the first few rows. 


``` r
summary(raw_dat)
```

```
##       ID               group               task                week        learnTime              time           item          
##  Length:6602        Length:6602        Length:6602        Min.   :1.000   Length:6602        Min.   :1.000   Length:6602       
##  Class :character   Class :character   Class :character   1st Qu.:1.000   Class :character   1st Qu.:2.000   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Median :1.000   Mode  :character   Median :3.000   Mode  :character  
##                                                           Mean   :1.498                      Mean   :2.504                     
##                                                           3rd Qu.:2.000                      3rd Qu.:4.000                     
##                                                           Max.   :2.000                      Max.   :4.000                     
##                                                                                                                                
##       acc               RT         
##  Min.   :0.0000   Min.   :  481.3  
##  1st Qu.:0.0000   1st Qu.:  986.0  
##  Median :0.0000   Median : 1326.7  
##  Mean   :0.3211   Mean   : 1758.0  
##  3rd Qu.:1.0000   3rd Qu.: 1950.0  
##  Max.   :1.0000   Max.   :17656.5  
##                   NA's   :4563
```

``` r
head(raw_dat)
```

```
## # A tibble: 6 × 9
##   ID       group task   week learnTime  time item       acc    RT
##   <chr>    <chr> <chr> <dbl> <chr>     <dbl> <chr>    <dbl> <dbl>
## 1 AmPm0cde GC    PNN       2 PM            1 agouti       0   NA 
## 2 AmPm0cde GC    PNN       1 AM            1 banyan       1 2422.
## 3 AmPm0cde GC    PNN       1 AM            1 baobab       0   NA 
## 4 AmPm0cde GC    PNN       2 PM            1 blenny       0   NA 
## 5 AmPm0cde GC    PNN       1 AM            1 caracal      1 1825.
## 6 AmPm0cde GC    PNN       1 AM            1 deglupta     0   NA
```
You don't need to worry too much about what these different variables mean for the purposes of this workshop, but you can see here that this is trial level data. The dependent variables are accuracy and RT, and we have these across several items for each participant. A full data dictionary is described below.

### Data dictionary

* **ID** --- a unique anonymised code for each participant  
* **group** --- whether participants were good comprehenders (GC) or poor comprehenders (PC)
* **task** --- which task this data comes from (all PNN)
* **learnTime** --- whether participants learned the items in the morning (AM) or evening (PM). This was a repeated measures manipulation---all participants completed both a morning and evening condition to compare sleep and wake effects on memory. 
* **time** --- whether the data are from test *1* (immediately after learning), test *2* (12 hours later), test **3** (24 hours later), or test *4* (one month later)  
* **item** --- the word trained and tested  
* **acc** --- whether the participant successfully recalled the word in the picture naming task  
* **RT** --- response time (ms), for accurate responses only  

# Select relevant variables using `select()`

Often our means of collecting data give us more information than we really care about. For example, Gorilla outputs all sorts of columns for screen size, browser etc. that won't be helpful for our analysis. We likely want to create a refined version of the dataset that includes on the variables of interest. 

To do this, we can use the `select()` function. Let's say for example that we only need the variables *ID, item* and *acc*. We can do this as follows: 


``` r
# Select by name
select_dat1 <- select(raw_dat, ID, item, acc)
```

In this code chunk, we create a new dataframe called *select_dat1*. The `select()` function takes the name of the dataframe you want to select from as the first argument (in this case, *raw_dat*). We then pass it the names of the variables we need. You should now see this dataframe in the environment pane - we still have all 6602 observations, but only the 3 variables we've specified. 

If you have lots of variables that you want to keep, you might want a quicker way of selecting lots of variables at once.Try running these two alternatives: 


``` r
# Select sequential columns by name
select_dat2 <- select(raw_dat, ID, item:RT)

# Select columns by column number
select_dat3 <- select(raw_dat, 1, 5:7, 9)
```

Compare the new dataframes (*select_dat2*, *select_dat3*) to your original one. What is the `:` doing? How have the numbers worked to select columns in the second example? 

Finally, it might sometimes be quicker just to specify the variables you *don't* need. You can do this by using a `-` sign in front of the variable name. For example, the task column is redundant here as we only have data from one task, so we can remove it as follows:


``` r
# Drop columns 
select_dat4 <- select(raw_dat, -task)
```

We've seen here some different ways that you can tidy your dataset for relevant variables that you need. Importantly, we've still kept our *original* dataframe (*raw_dat*), so we can always edit and re-run this code if there's something we've missed - be that by mistake as we go about writing our scripts, or further down the line  when a reviewer asks for an obscure piece of information you didn't think was relevant. Tidying our data in this way is fast and efficient, and avoids problems caused by trying to retrieve data and match it across raw and processed datasets. 

Before we move on, let's use your new column-selecting skills.

EXERCISE: Create a new data frame called selected_vars which includes the following variables: ID, learnTime, time, item, acc, RT




# Rename variables 

Once your dataset is a more manageable size, you might find that you want to rename your key variables. You can do this in two ways. 

If you want to rename all the variables in your dataset, you can type a list of all the different names in order. If you want to remind yourself of the existing names in order, you can use the names() function first. 

``` r
# Current names
names(raw_dat)
```

```
## [1] "ID"        "group"     "task"      "week"      "learnTime" "time"      "item"      "acc"       "RT"
```

``` r
# Renamed dataset
rename_dat1 <- set_names(raw_dat, c("ppt_ID", "read_group", "exp_task", "counterbalanced_order", "encode_time", "test_time", "word", "correct", "rt"))

# Check 
names(rename_dat1)
```

```
## [1] "ppt_ID"                "read_group"            "exp_task"              "counterbalanced_order" "encode_time"          
## [6] "test_time"             "word"                  "correct"               "rt"
```
Sometimes you might want to rename only certain variables. You can do this using the rename function, following the format new_name = old_name:


``` r
rename_dat2 <- rename(raw_dat, encode_time = learnTime, test_time = time)
```

If you inspect the new dataframe, you should be able to see that the two variables now have different names to before. 

EXERCISE 2: Create a new dataframe called renamed_vars, by renaming your selected_vars dataframe from Exercise 1. Edit the column names for the following variables: ID >> ppt_ID, learnTime >> learn_time, time >> test_time. 


# Filter for relevant data using `filter()`

Another typical step in data processing is to filter for the data we are interested in. For example, this dataset collected responses across two test sessions, but suppose we only want to analyse data from session one. We can create a reduced version of dataset for this purpose using the `filter()` function. 


``` r
filter_dat1 <- filter(raw_dat, time == 1)
```

Just as with the earlier functions, the first argument is the name of the dataframe that we want to filter (*raw_dat*). We then specify the conditions for retaining data: here that we want the `session` variable to be equal to (==) the value of 1. You can see in the environment menu that this *filter_dat1* dataframe should have appeared. We've kept all 9 variables as we haven't instructed otherwise, but we have approximately quarter of the number of observations now. If you inspect this dataframe by clicking the grid icon, you should see that we have dropped all of the trials from sessions 2-4. 

We can use different [logical operators](https://bookdown.org/rwnahhas/IntroToR/logical.html) to specify conditions in different ways. What do you think the following lines of code might do to the data? Then try running them to see if your predictions are correct. 


``` r
# Example 1
filter_dat2 <- filter(raw_dat, RT > 500)

# Example 2
filter_dat3 <- filter(raw_dat, RT <= 2000)

# Example 3
filter_dat4 <- filter(raw_dat, !is.na(RT))

# Example 4 - Character string
filter_dat4 <- filter(raw_dat, item == "agouti")

# Example 5 - Multiple criteria
filter_dat5 <- filter(raw_dat, time == 1 & RT <= 2000 )

# Example 6 - Multiple criteria
filter_dat6 <- filter(raw_dat, item == "banyan" | item == "caracal")
```

You can specify and combine your selection criteria in pretty much any way imaginable. Again, the key benefit here is that we haven't lost our original data at any point. Realise you've done something wrong? Edit and run it again. Need versions of your dataset with and without certain participants included? Create two versions. These all came from the same stored dataset (no deleting data you might regret!), and you've documented in code exactly what you've done with them. Having these steps scripted is a major win for reproducibility.  

# Combining data processing steps using the pipe `%>%` operator

So far, we've selected, renamed, and filtered our data in separate steps, reassigning the result to a new dataframe each time. This is fine, and very helpful when you are first developing your code as it allows you to inspect/test each step as you go and check things are running as expected. 

However, in the long run, this can get a bit messy as we end up with lots of different dataframes in the environment. It also becomes increasingly difficult to name them all something sensible, and it's easy to lose track and make mistakes of which dataframe we are suppose to be using. 

To counter this problem, the tidyverse has a handy function called the pipe operator. It looks like this: `%>%`, or alternatively like this `|>`. You can read the pipe operator as "and then". It takes the output from the code to the left of it, *and then* sends it to the first argument of your next line of code. Remember that the first argument of the select() and filter() functions was the dataframe, so this works very nicely. 

For example, we can select our variables of interest then filter for the observations we want, all in a single step as follows: 


``` r
select_filter_dat <- raw_dat %>%
  select(ID, time, item, RT) %>% 
  rename(test_time = time) %>% 
  filter(RT < 3000)
```

So you can read the above chunk as follows: create a new dataframe called *select_filter_dat* by taking the *raw_dat* dataframe AND THEN selecting relevant variables (ID, test, item, RT) AND THEN renaming test to test_time AND THEN filtering for only those trials where the RT was less than 3000 ms. We haven't needed to rename the dataframe at each stage, as the pipe has filled this in for us from the previous set. 


# Calculate participant averages using `group_by()` and `summarise()`

One thing you often want to do with your data is calculate average scores for each participant. The tidyverse makes it very easy to do this. We make use of the `group_by()` function to "group" our scores by participant ID, then the `summarise()` function to calculate some kind of summary score. For example:


``` r
ppt_average1 <- raw_dat %>% 
  group_by(ID) %>% 
  summarise(mean_acc = mean(acc))
```

If you inspect the dataframe, you can see that we now have one row per participant, and that we have 
summarised the accuracy scores into a mean for each one.We called our new summary variable mean_acc, and specified how it should be calculated using the mean function. We can also ask for an average RT at the same time by adding it as another argument to the summary function. 


``` r
ppt_average1 <- raw_dat %>% 
  group_by(ID) %>% 
  summarise(mean_acc = mean(acc),
            mean_RT = mean(RT))
```

What do you notice about the averaged RT variable? 


``` r
ppt_average1 <- raw_dat %>% 
  group_by(ID) %>% 
  summarise(mean_acc = mean(acc),
            mean_RT = mean(RT, na.rm = TRUE))
```

Importantly, we can use more than one variable in the `group_by()` to calculate participant averages for each condition. Let's work out how participants do at each of the four time points. 


``` r
ppt_average2 <- raw_dat %>% 
  group_by(ID, time) %>% 
  summarise(mean_acc = mean(acc),
            mean_RT = mean(RT, na.rm = TRUE))
```

```
## `summarise()` has grouped output by 'ID'. You can override using the `.groups` argument.
```
You can use the same principles to produce group summary statistics at each test point. For example, to calculate the mean and SD for accuracy scores: 


``` r
ppt_average2 %>% 
  group_by(time) %>% 
  summarise(ppt_mean_acc = mean(mean_acc),
            ppt_sd_acc = sd(mean_acc))
```

```
## # A tibble: 4 × 3
##    time ppt_mean_acc ppt_sd_acc
##   <dbl>        <dbl>      <dbl>
## 1     1        0.275      0.169
## 2     2        0.334      0.176
## 3     3        0.404      0.207
## 4     4        0.273      0.178
```

EXERCISE: Calculate group summary statistics on mean RT scores at each test point. You can use the previous code as a starting point. Work out the mean and standard deviation, then if you have time you can also add the minimum and maximum response times. 

# Create new variables

Our final function today showcases how we can create new variables based on our existing data. As a simple example, let's say we want to summarise our participant averages as percentages rather than proportions. To do this, we would need to multiple the mean accuracy value by 100. We can do this using the `mutate()` function. 


``` r
ppt_average3 <- ppt_average2 %>% 
  mutate(percent_correct = mean_acc*100)
```

We've taken our data frame of participant averages, and used mutate to create a new variable called percent_correct. Again, we have the new name on the left, with details on how to produce it on the right. We specify that the variable percent_correct should be calculated by multiplying mean_acc by 100. We can see that the new data frame now has one more variable, and can check that it's doing as we expect. 

Let's try adding another example. Reaction time data are often skewed, and require some kind of transformation to aid analysis. One such transformation is the inverse, which we calculate using 1/RT value. Let's add this to the above example. 


``` r
ppt_average3 <- ppt_average2 %>% 
  mutate(percent_correct = mean_acc*100,
         inv_RT = 1/mean_RT)
```

# Putting it all together 

## Combined data processing steps

Today we have learned how to load data, select and rename relevant variables, filter out irrelevant cases, summarise across participants/variables, 

As a final exercise, let's create a processed dataset that can show us whether windows of sleep or wake are better for picture naming. 

EXERCISE: Create a dataset called sleep_wake by chaining the following steps together: (1) take the raw data; (2) include only ID, learnTime, time, acc, and RT (renaming if you choose); (3) subset the data to include the first two test times only; (4) create a log-transformed RT variable (log_RT), by using the log() function on RT; and (5) group by participant in calculating average accuracies, raw RTs and log-RTs for each participant. 

Once you have done this, print the whole group summaries by learnTime and time. How does performance change between time 1 and 2 when participants learn in the PM versus the AM? 


``` r
sleep_wake <- raw_dat %>% 
  select(ID, learnTime, time, acc, RT) %>% 
  filter(time == 1 | time == 2) %>% 
  mutate(log_RT = log(RT)) %>% 
  group_by(ID, learnTime, time) %>% 
  summarise(acc = mean(acc), RT = mean(RT, na.rm = TRUE), log_RT = mean(log_RT, na.rm = TRUE))
```

```
## `summarise()` has grouped output by 'ID', 'learnTime'. You can override using the `.groups` argument.
```

``` r
sleep_wake %>% 
  group_by(learnTime, time) %>% 
  summarise(mean_acc = mean(acc), mean_RT = mean(RT, na.rm = TRUE), mean_logRT = mean(log_RT, na.rm = TRUE))
```

```
## `summarise()` has grouped output by 'learnTime'. You can override using the `.groups` argument.
```

```
## # A tibble: 4 × 5
## # Groups:   learnTime [2]
##   learnTime  time mean_acc mean_RT mean_logRT
##   <chr>     <dbl>    <dbl>   <dbl>      <dbl>
## 1 AM            1    0.316   2019.       7.46
## 2 AM            2    0.291   2071.       7.46
## 3 PM            1    0.241   2100.       7.48
## 4 PM            2    0.376   1537.       7.22
```

## Saving datasets

We've now created a tidied dataset of participant averages for the variables of interest. In some cases, it might make sense to save this processed dataset so that we don't have to re-run this script each time. Let's save it back to the data folder, giving it a new name to clarify that it's the processed version. 


``` r
write.csv(sleep_wake, "./data/picName_processed.csv", row.names = FALSE)
```

## Saving annotated analysis files 



# Beyond today

Today we have learned how to load data, select and rename relevant variables, filter for relevant observations, create new variables, summarise across them, and save processed datasets for future use (phew!). In the end, we saw how efficiently you can run and re-run this with just a few lines of code chained together. 

The tidyverse has plenty of useful tools for data wrangling beyond those we've explored today. For example, `pivot_longer()` and `pivot_wider()` are useful tools for transforming your data between wide (one row per participant, with each column representing a different trial) and long (one row per trial per participant) formats. Functions such as `left_join()` allow us to link data between different dataframes based on the participant ID. There are helpful tools for parsing character strings, or processing dates and times. You name it! 





