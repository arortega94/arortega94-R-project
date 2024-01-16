# Data transformation using R and R Studio
## Data cleaning, processing, analyzing, visualizing and sharing using R Studio for a **shared bikes** company
### Context
I spearheaded a pivotal project aimed at **optimizing annual memberships** for sustained business success. I meticulously analyzed historical bike ride data from the entirety of 2019 in Chicago. My primary responsibility involved dissecting the differences between **occasional riders and annual members**, with the overarching goal of formulating data-driven marketing strategies. The resulting case study not only showcases my adeptness in extracting insights from data but also highlights my ability to develop strategic recommendations and present compelling visualizations. This project serves as a concrete representation of my analytical proficiency and strategic acumen **using R for data analysis**.

### Steps
1. Preparing the enviroment
2. Collecting data
3. Wrangling and merging multiple datasets into a master one
4. Cleaning up and preparing data for analysis
5. Conducting descriptive analysis
6. Creating visualizations

## 1. Preparing the enviroment
For this analysis we will be using the package **tidyverse**, so we will install it and load it.
```r
install.packages("tidyverse")
library(tidyverse)

> library(tidyverse)
── Attaching core tidyverse packages ───────────────────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.4     ✔ readr     2.1.4
✔ forcats   1.0.0     ✔ stringr   1.5.1
✔ ggplot2   3.4.4     ✔ tibble    3.2.1
✔ lubridate 1.9.3     ✔ tidyr     1.3.0
✔ purrr     1.0.2     
── Conflicts ─────────────────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package to force all conflicts to become errors
```
## 2. Collecting data
The data is contained in 4 different files, one for each quarter of 2019. After downloading the files, we need to load them into R using the function *read_csv()*. We will add the data into 4 different data frames.

```r
q1_2019 <- read_csv("~/trip_data_2019/2019_Q1_trips.csv")
q2_2019 <- read_csv("~/trip_data_2019/2019_Q2_trips.csv")
q3_2019 <- read_csv("~/trip_data_2019/2019_Q3_trips.csv")
q4_2019 <- read_csv("~/trip_data_2019/2019_Q4_trips.csv")
```
No we have all 4 different data frames loaded in R. Let´s conduct a basic data inspection using data investigation functions:

```r
str(q1_2019)
tibble [365,069 × 12] (S3: tbl_df/tbl/data.frame)
 $ ride_id           : chr [1:365069] "21742443" "21742444" "21742445" "21742446" ...
 $ started_at        : POSIXct[1:365069], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" "2019-01-01 00:13:23" ...
 $ ended_at          : POSIXct[1:365069], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" "2019-01-01 00:27:12" ...
 $ rideable_type     : chr [1:365069] "2167" "4386" "1524" "252" ...
 $ tripduration      : num [1:365069] 390 441 829 1783 364 ...
 $ start_station_id  : num [1:365069] 199 44 15 123 173 98 98 211 150 268 ...
 $ start_station_name: chr [1:365069] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
 $ end_station_id    : num [1:365069] 84 624 644 176 35 49 49 142 148 141 ...
 $ end_station_name  : chr [1:365069] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
 $ member_casual     : chr [1:365069] "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
 $ gender            : chr [1:365069] "Male" "Female" "Female" "Male" ...
 $ birthyear         : num [1:365069] 1989 1990 1994 1993 1994 ...
```
## 3. Wrangling and merging multiple datasets into a master one
To merge all frames into a single one for analysis, we need to make sure data integrity. Let's check the column names of the data sets:
```r
colnames(q3_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"         
> colnames(q4_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"         
> colnames(q2_2019)
 [1] "01 - Rental Details Rental ID"            "01 - Rental Details Local Start Time"
 [3] "01 - Rental Details Local End Time"       "01 - Rental Details Bike ID"     
 [5] "01 - Rental Details Duration In Seconds Uncapped"       "03 - Rental Start Station ID"
 [7] "03 - Rental Start Station Name" "02 - Rental End Station ID"    
 [9] "02 - Rental End Station Name"   "User Type"      "Member Gender"    "05 - Member Details Member Birthday Year"         
> colnames(q1_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"
```
As we can see, some column names, differ, so let's rename all of them as the ones in Q1 for consistency:
```r
(q4_2019 <- rename(q4_2019
                   ,ride_id = trip_id
                   ,started_at = start_time  
                   ,ended_at = end_time
                   ,rideable_type = bikeid
                   ,tripduration = tripduration
                   ,start_station_id = from_station_id  
                   ,start_station_name = from_station_name
                   ,end_station_id = to_station_id 
                   ,end_station_name = to_station_name,
                   ,member_casual = usertype
                   ,gender = gender
                   ,birthyear = birthyear))

(q3_2019 <- rename(q3_2019
                   ,ride_id = trip_id
                   ,started_at = start_time  
                   ,ended_at = end_time
                   ,rideable_type = bikeid
                   ,tripduration = tripduration
                   ,start_station_id = from_station_id  
                   ,start_station_name = from_station_name
                   ,end_station_id = to_station_id 
                   ,end_station_name = to_station_name,
                   ,member_casual = usertype
                   ,gender = gender
                   ,birthyear = birthyear))

(q2_2019 <- rename(q2_2019
                   ,ride_id = "01 - Rental Details Rental ID"
                   ,started_at = "01 - Rental Details Local Start Time"
                   ,ended_at = "01 - Rental Details Local End Time"
                   ,rideable_type = "01 - Rental Details Bike ID"  
                   ,tripduration = "01 - Rental Details Duration In Seconds Uncapped"
                   ,start_station_id = "03 - Rental Start Station ID"
                   ,start_station_name = "03 - Rental Start Station Name"
                   ,end_station_id = "02 - Rental End Station ID" 
                   ,end_station_name = "02 - Rental End Station Name"
                   ,member_casual = "User Type"
                   ,gender = "Member Gender"
                   ,birthyear = "05 - Member Details Member Birthday Year"))
(q1_2019 <- rename(q1_2019
                   ,ride_id = trip_id
                   ,started_at = start_time  
                   ,ended_at = end_time
                   ,rideable_type = bikeid
                   ,tripduration = tripduration
                   ,start_station_id = from_station_id  
                   ,start_station_name = from_station_name
                   ,end_station_id = to_station_id 
                   ,end_station_name = to_station_name,
                   ,member_casual = usertype
                   ,gender = gender
                   ,birthyear = birthyear))
```
Now, let's check back again:
```r
> colnames(q3_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"         
> colnames(q4_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"         
> colnames(q2_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"         
> colnames(q1_2019)
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"
```
And finally, let's merge all the datasets into one called "all_trips":
```r
all_trips <- bind_rows(q1_2019, q2_2019, q3_2019, q4_2019)
```
We can see the file merge correctly, and is now a file which contains 13 columns and 3,818,004 observations.
```r
glimpse(all_trips)
Rows: 3,818,004
Columns: 13
$ ride_id            <chr> "21742443", "21742444", "21742445", "21742446", "21742447", "217…
$ started_at         <dttm> 2019-01-01 00:04:37, 2019-01-01 00:08:13, 2019-01-01 00:13:23, …
$ ended_at           <dttm> 2019-01-01 00:11:07, 2019-01-01 00:15:34, 2019-01-01 00:27:12, …
$ rideable_type      <chr> "2167", "4386", "1524", "252", "1170", "2437", "2708", "2796", "…
$ tripduration       <dbl> 390, 441, 829, 1783, 364, 216, 177, 100, 1727, 336, 886, 653, 60…
$ start_station_id   <dbl> 199, 44, 15, 123, 173, 98, 98, 211, 150, 268, 299, 204, 90, 90, …
$ start_station_name <chr> "Wabash Ave & Grand Ave", "State St & Randolph St", "Racine Ave …
$ end_station_id     <dbl> 84, 624, 644, 176, 35, 49, 49, 142, 148, 141, 295, 420, 255, 255…
$ end_station_name   <chr> "Milwaukee Ave & Grand Ave", "Dearborn St & Van Buren St (*)", "…
$ member_casual      <chr> "member", "member", "member", "member", "member", "member", "mem…
$ gender             <chr> "Male", "Female", "Female", "Male", "Male", "Female", "Male", "M…
$ birthyear          <dbl> 1989, 1990, 1994, 1993, 1994, 1983, 1984, 1990, 1995, 1996, 1994…
$ date               <date> 2019-01-01, 2019-01-01, 2019-01-01, 2019-01-01, 2019-01-01, 201…
```
## 4. Cleaning up and preparing data for analysis
Let's inspect the data thoroughly to clean up any inconsistencies with the data or the observations. We will use the following functions:
```r
colnames(all_trips)  #List of column names
nrow(all_trips)  #How many rows are in data frame?
dim(all_trips)  #Dimensions of the data frame?
head(all_trips)  #See the first 6 rows of data frame
tail(all_trips) #See the last rows
str(all_trips)  #See list of columns and data types (numeric, character, etc)
summary(all_trips)  #Statistical summary of data. Mainly for numerics

colnames(all_trips)  #List of column names
 [1] "ride_id"            "started_at"         "ended_at"           "rideable_type"     
 [5] "tripduration"       "start_station_id"   "start_station_name" "end_station_id"    
 [9] "end_station_name"   "member_casual"      "gender"             "birthyear"         
[13] "date"

> nrow(all_trips)  #How many rows are in data frame?
[1] 3818004

> dim(all_trips)  #Dimensions of the data frame?
[1] 3818004      13

> head(all_trips)  #See the first 6 rows of data frame
# A tibble: 6 × 18
  ride_id started_at          ended_at            rideable_type tripduration start_station_id
  <chr>   <dttm>              <dttm>              <chr>                <dbl>            <dbl>
1 217424… 2019-01-01 00:04:37 2019-01-01 00:11:07 2167                   390              199
2 217424… 2019-01-01 00:08:13 2019-01-01 00:15:34 4386                   441               44
3 217424… 2019-01-01 00:13:23 2019-01-01 00:27:12 1524                   829               15
4 217424… 2019-01-01 00:13:45 2019-01-01 00:43:28 252                   1783              123
5 217424… 2019-01-01 00:14:52 2019-01-01 00:20:56 1170                   364              173
6 217424… 2019-01-01 00:15:33 2019-01-01 00:19:09 2437                   216               98
# ℹ 7 more variables: start_station_name <chr>, end_station_id <dbl>,
#   end_station_name <chr>, member_casual <chr>, gender <chr>, birthyear <dbl>, date <date>

> tail(all_trips) #See the last rows
# A tibble: 6 × 13
  ride_id started_at          ended_at            rideable_type tripduration start_station_id
  <chr>   <dttm>              <dttm>              <chr>                <dbl>            <dbl>
1 259628… 2019-12-31 23:54:54 2020-01-01 00:22:02 5996                  1628              145
2 259629… 2019-12-31 23:56:13 2020-01-01 00:15:45 2196                  1172              112
3 259629… 2019-12-31 23:56:34 2020-01-01 00:22:08 4877                  1533               90
4 259629… 2019-12-31 23:57:05 2020-01-01 00:05:46 863                    520              623
5 259629… 2019-12-31 23:57:11 2020-01-01 00:05:45 2637                   514              623
6 259629… 2019-12-31 23:57:17 2019-12-31 23:59:18 5930                   120              256
# ℹ 7 more variables: start_station_name <chr>, end_station_id <dbl>,
#   end_station_name <chr>, member_casual <chr>, gender <chr>, birthyear <dbl>, date <date>

> str(all_trips)  #See list of columns and data types (numeric, character, etc)
tibble [3,818,004 × 13] (S3: tbl_df/tbl/data.frame)
 $ ride_id           : chr [1:3818004] "21742443" "21742444" "21742445" "21742446" ...
 $ started_at        : POSIXct[1:3818004], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" "2019-01-01 00:13:23" ...
 $ ended_at          : POSIXct[1:3818004], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" "2019-01-01 00:27:12" ...
 $ rideable_type     : chr [1:3818004] "2167" "4386" "1524" "252" ...
 $ tripduration      : num [1:3818004] 390 441 829 1783 364 ...
 $ start_station_id  : num [1:3818004] 199 44 15 123 173 98 98 211 150 268 ...
 $ start_station_name: chr [1:3818004] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
 $ end_station_id    : num [1:3818004] 84 624 644 176 35 49 49 142 148 141 ...
 $ end_station_name  : chr [1:3818004] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
 $ member_casual     : chr [1:3818004] "member" "member" "member" "member" ...
 $ gender            : chr [1:3818004] "Male" "Female" "Female" "Male" ...
 $ birthyear         : num [1:3818004] 1989 1990 1994 1993 1994 ...
 $ date              : Date[1:3818004], format: "2019-01-01" "2019-01-01" "2019-01-01" ...

> summary(all_trips)  #Statistical summary of data. Mainly for numerics
   ride_id            started_at                        ended_at                     
 Length:3818004     Min.   :2019-01-01 00:04:37.00   Min.   :2019-01-01 00:11:07.00  
 Class :character   1st Qu.:2019-05-29 15:49:26.50   1st Qu.:2019-05-29 16:09:28.25  
 Mode  :character   Median :2019-07-25 17:50:54.00   Median :2019-07-25 18:12:23.00  
                    Mean   :2019-07-19 21:47:37.11   Mean   :2019-07-19 22:11:47.56  
                    3rd Qu.:2019-09-15 06:48:05.75   3rd Qu.:2019-09-15 08:30:13.25  
                    Max.   :2019-12-31 23:57:17.00   Max.   :2020-01-21 13:54:35.00  
                                                                                     
 rideable_type       tripduration      start_station_id start_station_name end_station_id 
 Length:3818004     Min.   :      61   Min.   :  1.0    Length:3818004     Min.   :  1.0  
 Class :character   1st Qu.:     411   1st Qu.: 77.0    Class :character   1st Qu.: 77.0  
 Mode  :character   Median :     709   Median :174.0    Mode  :character   Median :174.0  
                    Mean   :    1450   Mean   :201.7                       Mean   :202.6  
                    3rd Qu.:    1283   3rd Qu.:289.0                       3rd Qu.:291.0  
                    Max.   :10628400   Max.   :673.0                       Max.   :673.0  
                                                                                          
 end_station_name   member_casual         gender            birthyear     
 Length:3818004     Length:3818004     Length:3818004     Min.   :1759    
 Class :character   Class :character   Class :character   1st Qu.:1979    
 Mode  :character   Mode  :character   Mode  :character   Median :1987    
                                                          Mean   :1984    
                                                          3rd Qu.:1992    
                                                          Max.   :2014    
                                                          NA's   :538751  
      date                
 Min.   :2019-01-01   
 1st Qu.:2019-05-29 
 Median :2019-07-25   
 Mean   :2019-07-19                                                           
 3rd Qu.:2019-09-15                                                           
 Max.   :2019-12-31                                                           
                                                                               
```
For clarity purposes, let's change the "member" to "Subscriber" and the "casual" to "Customer":
```r
all_trips <-  all_trips %>% 
  mutate(member_casual = recode(member_casual
                                ,"Subscriber" = "member"
                                ,"Customer" = "casual"))
# Then, let's check the number of observations is correct:
> table(all_trips$member_casual)

 Customer  Subscriber 
 880637    2937367
```
Now, for granularity, let's add several columns with the year, month and date for each ride separately:
```r
all_trips$date <- as.Date(all_trips$started_at) #The default format is yyyy-mm-dd
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$year <- format(as.Date(all_trips$date), "%Y")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```
And then let's add a ride length calculation in seconds for further analysis:
```r
all_trips$ride_length <- difftime(all_trips$ended_at,all_trips$started_at, units = "secs")
```
Now, let's convert the data type of ride_length to numeric so we can run calculations on the data:
```r
is.factor(all_trips$ride_length)
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)
```
And let's check the structure of the data set for safety:
```r
> str(all_trips)
tibble [3,818,004 × 18] (S3: tbl_df/tbl/data.frame)
 $ ride_id           : chr [1:3818004] "21742443" "21742444" "21742445" "21742446" ...
 $ started_at        : POSIXct[1:3818004], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" ...
 $ ended_at          : POSIXct[1:3818004], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" ...
 $ rideable_type     : chr [1:3818004] "2167" "4386" "1524" "252" ...
 $ tripduration      : num [1:3818004] 390 441 829 1783 364 ...
 $ start_station_id  : num [1:3818004] 199 44 15 123 173 98 98 211 150 268 ...
 $ start_station_name: chr [1:3818004] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
 $ end_station_id    : num [1:3818004] 84 624 644 176 35 49 49 142 148 141 ...
 $ end_station_name  : chr [1:3818004] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
 $ member_casual     : chr [1:3818004] "member" "member" "member" "member" ...
 $ gender            : chr [1:3818004] "Male" "Female" "Female" "Male" ...
 $ birthyear         : num [1:3818004] 1989 1990 1994 1993 1994 ...
 $ date              : Date[1:3818004], format: "2019-01-01" "2019-01-01" ...
 $ month             : chr [1:3818004] "01" "01" "01" "01" ...
 $ day               : chr [1:3818004] "01" "01" "01" "01" ...
 $ year              : chr [1:3818004] "2019" "2019" "2019" "2019" ...
 $ day_of_week       : chr [1:3818004] "martes" "martes" "martes" "martes" ...
 $ ride_length       : num [1:3818004] 390 441 829 1783 364 ...
```
And finally, before running the analysis, I realised there are some observations when bikes were taken out of docks and checked for quality by Divvy or ride_length was negative. Since some data is being removed, we will create a second version of the data frame:
```r
all_trips_v2 <- all_trips[!(all_trips$start_station_name == "HQ QR" | all_trips$ride_length<0),]
```
## 5. Conducting descriptive analysis
## 6. Creating visualizations
