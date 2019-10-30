---
author: Ethan Chan
date: 2019-10-29T21:13:14-05:00
linktitle: Joining Data in R with dplyr
title: Joining Data in R with dplyr
weight: 10
categories: ["R"]
tags: ["R", "data wrangling", "dplyr"]
---


## Introduction

This tutorial will teach you how to join different datasets together in R using the "join" functions of the [dplyr](https://www.rdocumentation.org/packages/dplyr) package by Hadley Wickham.

We will be using the nycflights13 package for this exercise, which contains airline on-time data for all flights departing NYC airports in 2013. For further documentation about this package, please see [nycflights13](https://www.rdocumentation.org/packages/dplyr)

Let's begin by loading the nycflights13 package and the flights table from nycflights13.

```
library(nycflights13)
names(flights) #checking for all variable names in this dataframe

#  [1] "year"           "month"          "day"           
#  [4] "dep_time"       "sched_dep_time" "dep_delay"     
#  [7] "arr_time"       "sched_arr_time" "arr_delay"     
# [10] "carrier"        "flight"         "tailnum"       
# [13] "origin"         "dest"           "air_time"      
# [16] "distance"       "hour"           "minute"        
# [19] "time_hour"    

head(flights) #let's take a peek into this dataframe

# # A tibble: 6 x 19
#    year month   day dep_time sched_dep_time dep_delay arr_time
#   <int> <int> <int>    <int>          <int>     <dbl>    <int>
# 1  2013     1     1      517            515         2      830
# 2  2013     1     1      533            529         4      850
# 3  2013     1     1      542            540         2      923
# 4  2013     1     1      544            545        -1     1004
# 5  2013     1     1      554            600        -6      812
# 6  2013     1     1      554            558        -4      740
# # ... with 12 more variables: sched_arr_time <int>,
# #   arr_delay <dbl>, carrier <chr>, flight <int>,
# #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>,
# #   distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

str(flights) #checking the flights dataframe further

# Classes ‘tbl_df’, ‘tbl’ and 'data.frame':	336776 obs. of  19 variables:
#  $ year          : int  2013 2013 2013 2013 2013 2013 2013 2013 2013 2013 ...
#  $ month         : int  1 1 1 1 1 1 1 1 1 1 ...
#  $ day           : int  1 1 1 1 1 1 1 1 1 1 ...
#  $ dep_time      : int  517 533 542 544 554 554 555 557 557 558 ...
#  $ sched_dep_time: int  515 529 540 545 600 558 600 600 600 600 ...
#  $ dep_delay     : num  2 4 2 -1 -6 -4 -5 -3 -3 -2 ...
#  $ arr_time      : int  830 850 923 1004 812 740 913 709 838 753 ...
#  $ sched_arr_time: int  819 830 850 1022 837 728 854 723 846 745 ...
#  $ arr_delay     : num  11 20 33 -18 -25 12 19 -14 -8 8 ...
#  $ carrier       : chr  "UA" "UA" "AA" "B6" ...
#  $ flight        : int  1545 1714 1141 725 461 1696 507 5708 79 301 ...
#  $ tailnum       : chr  "N14228" "N24211" "N619AA" "N804JB" ...
#  $ origin        : chr  "EWR" "LGA" "JFK" "JFK" ...
#  $ dest          : chr  "IAH" "IAH" "MIA" "BQN" ...
#  $ air_time      : num  227 227 160 183 116 150 158 53 140 138 ...
#  $ distance      : num  1400 1416 1089 1576 762 ...
#  $ hour          : num  5 5 5 5 6 5 6 6 6 6 ...
#  $ minute        : num  15 29 40 45 0 58 0 0 0 0 ...
#  $ time_hour     : POSIXct, format: "2013-01-01 05:00:00" ...

```
## What do these carrier codes mean?

We've got some nice data here about flight departures from NYC airports in 2013. What if we were interested in finding out the carrier names from our data? If you live in the United States, it is common knowledge that "UA" stands for United Airlines, but we want to find out what all of these carrier codes translate to! Let's continue further by looking into the airlines table, which contain the carrier codes and full airline names.

```
names(airlines)
# [1] "carrier" "name"

head(airlines)
# # A tibble: 6 x 2
#   carrier name                    
#   <chr>   <chr>                   
# 1 9E      Endeavor Air Inc.       
# 2 AA      American Airlines Inc.  
# 3 AS      Alaska Airlines Inc.    
# 4 B6      JetBlue Airways         
# 5 DL      Delta Air Lines Inc.    
# 6 EV      ExpressJet Airlines Inc.

```

## Joining the data

How do we combine the data from our two datasets, flights and airlines, so we can immediately identify the airline names with flight departure data? Here is where the "join" comes in!

We have identified a common key across the flights and airlines dataset. The variable "carrier" exists in both datasets and we can join the data together using this key. Each carrier is represented by a 2-character code in both the flights and airlines dataset.

We will be using the inner_join() function for this exerise. Let's create a new dataframe called "flightsJoined" for this join.

```
flightsJoined <- flights %>%
  inner_join(airlines, by = c('carrier' = 'carrier'))
```
The general usage for joins in dplyr are as follows:

inner_join(x, y, by = c(".x", ".y"))

x is your first dataset or object.

y is your second dataset or object that you want to join with x.

.x is the variable name from x that you want to join with y on.

.y is the variable name from y that you want to join with x on. 

Fortunately, the variable names representing carrier are the same in flights and airlines. This may not always be the case, but you can join data together using different variable names if you have to!

Let's take a look at our flightsJoined dataset

```
flightsJoined %>%
  select(carrier, name, flight, origin, dest) #selecting a subset of variables from flightsJoined

# # A tibble: 336,776 x 5
#    carrier name                     flight origin dest 
#    <chr>   <chr>                     <int> <chr>  <chr>
#  1 UA      United Air Lines Inc.      1545 EWR    IAH  
#  2 UA      United Air Lines Inc.      1714 LGA    IAH  
#  3 AA      American Airlines Inc.     1141 JFK    MIA  
#  4 B6      JetBlue Airways             725 JFK    BQN  
#  5 DL      Delta Air Lines Inc.        461 LGA    ATL  
#  6 UA      United Air Lines Inc.      1696 EWR    ORD  
#  7 B6      JetBlue Airways             507 EWR    FLL  
#  8 EV      ExpressJet Airlines Inc.   5708 LGA    IAD  
#  9 B6      JetBlue Airways              79 JFK    MCO  
# 10 AA      American Airlines Inc.      301 LGA    ORD  
# # ... with 336,766 more rows

```

Now we have a new dataframe named flightsJoined that contains the carrier, full airline name and departure data! This will make our data analysis much easier to decipher and interpret.

Users of Microsoft Excel will immediately draw comparisons between an inner_join in R with a VLOOKUP (or my preferred, INDEX MATCH) in Excel. You're not incorrect! Both functions serve the same purpose. 

A caveat of inner_join() is that it only returns rows that have matches in the tables you're joining. We want to make sure that we didn't lose any data from our inner join. Let's do a double-check.

```
nrow(flights) #checking for the number of rows in flights

# [1] 336776

nrow(flightsJoined) #checking for the number of rows in flightsJoined

# [1] 336776

```
Since every row in the flights dataframe has a carrier code valued, we returned the same number of rows in our flightsJoined dataframe. We are sure we did not lose any data in the process.

## Other joins

SQL users will be right at home here. The joins you have in SQL are available in the dplyr package as well! 

Let's use left_join() as an example. A left join returns all the rows of the first table, even when there is not a matching value in the second table. Rows in x without a corresponding match in y will be returned with a NA. If there are multiple matches between x and y, every combination will be returned. 

Let's compare the difference between inner_join() and left_join() next. We will be using the airports dataframe from the nycflights13 package.

```
head(airports) #checking rows of airports

# A tibble: 6 x 8
  faa   name                             lat   lon   alt    tz dst   tzone           
  <chr> <chr>                          <dbl> <dbl> <dbl> <dbl> <chr> <chr>           
1 04G   Lansdowne Airport               41.1 -80.6  1044    -5 A     America/New_York
2 06A   Moton Field Municipal Airport   32.5 -85.7   264    -6 A     America/Chicago 
3 06C   Schaumburg Regional             42.0 -88.1   801    -6 A     America/Chicago 
4 06N   Randall Airport                 41.4 -74.4   523    -5 A     America/New_York
5 09J   Jekyll Island Airport           31.1 -81.4    11    -5 A     America/New_York
6 0A9   Elizabethton Municipal Airport  36.4 -82.2  1593    -5 A     America/New_York
```

The airports dataframe contains airport information based on on the FAA code for each airport. This is very valuable information to determine the full destination airport names from flights. 

What if we were only interested in destination airports in the Eastern Time Zone? Let's filter the airports data to only capture airports in the Eastern Time Zone, defined by tz = -5. We'll create a new dataframe named airportsET

```
airportsET <- filter(airports, tz == -5)

nrow(airports) #checking rows of data in original airports dataframe
# [1] 1458

nrow(airportsET) #checking rows of data in airportsET dataframe
# [1] 521
```
We are left with 521 rows in airportsET as compared to the 1458 rows in airports. Let's try an inner join and left join using airportsET and flights to illustrate the differences between the two joins. This time around, our variable names don't match between airportsET and flights, but that's okay! The "dest" variable in flights contains FAA codes for our destination airports. The "faa" variable in airports contains the same FAA codes. We can join on these two variables from flights and airports.

We'll create a new dataframe named nycDestsET to capture flights departing NYC airports to Eastern Time Zone airport destinations. 

```
nycDestsET <- flights %>%
  inner_join(airportsET, by = c("dest" = "faa"))
  
nrow(flights)
# [1] 336776

nrow(nycDestsET)
# [1] 192377
```

Our inner join of Eastern Time Zone airports, airportsET, with flights only returned 192377 rows as compared to the 336776 rows in the original flights dataframe. Let's try out the left join! We'll name the left joined dataframe nycDestsETLeft

```

nycDestsETLeft <- flights %>%
  left_join(airportsET, by = c("dest" = "faa"))

nrow(nycDestsETLeft) #total number of rows in nycDestsETLeft
# [1] 336776

sum(is.na(nycDestsETLeft$name)) #total number of rows containing NA for airport names
# [1] 144399
```

Let's see what happened here. Our left joined dataframe, nycDestsETLeft, contains the same number of rows (336776) as the original flights dataframe. But how many airport names came back as NA in nycDestsETLeft? We counted a total of 144399 flights without a matching destination airport name. 

What could this mean? Logically, this COULD mean that the flights never made it to their destination airport. A more appropriate speculation would be that flights that were bound for non-Eastern Time Zone airports returned a NA for airport name!

Let's take a closer look.

```
head(nycDestsETLeft %>% select(flight, dep_time, arr_time, air_time, dest, name, tz))
# A tibble: 6 x 7
  flight dep_time arr_time air_time dest  name                               tz
   <int>    <int>    <int>    <dbl> <chr> <chr>                           <dbl>
1   1545      517      830      227 IAH   NA                                 NA
2   1714      533      850      227 IAH   NA                                 NA
3   1141      542      923      160 MIA   Miami Intl                         -5
4    725      544     1004      183 BQN   NA                                 NA
5    461      554      812      116 ATL   Hartsfield Jackson Atlanta Intl    -5
6   1696      554      740      150 ORD   NA                                 NA
```

Our left joined dataset returned a NA for airport name for any flights that were destined to non-Eastern Time Zone airports! We can observe the fundamental differences between a left join and inner join here. 

Key Takeaways:
Always check your data after an inner join to ensure you did not exclude any data! 







