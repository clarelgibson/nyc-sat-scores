nyc-sat-scores
================
Clare Gibson
13/05/2021

# NYC SAT Scores

## Setup

First we will load the libraries we need for this analysis.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.3     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.0     ✓ dplyr   1.0.5
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## Scope

*Amended from the [Dataquest tutorial
blog](https://www.dataquest.io/blog/data-science-portfolio-project/ "Data Science Portfolio Tutorial by Dataquest.io")*
In this project, we look at the [SAT
scores](https://en.wikipedia.org/wiki/SAT "SAT wiki") of high schoolers
in New York City, along with various demographic and other information
about them. The SAT, or Scholastic Aptitude Test, is a test that high
schoolers take in the US before applying to college. Colleges take the
test scores into account when making admissions decisions, so it’s
fairly important to do well on. The test is divided into 3 sections,
each of which is scored out of 800 points. The total score is out of
2400 (although this has changed back and forth a few times, the scores
in this dataset are out of 2400). High schools are often ranked by their
average SAT scores, and high SAT scores are considered a sign of how
good a school district is.

There have been
[allegations](https://www.brookings.edu/research/race-gaps-in-sat-scores-highlight-inequality-and-hinder-upward-mobility/ "Race gaps in SAT scores")
about the SAT being unfair to certain racial groups in the US, so doing
this analysis on New York City data will help shed some light on the
fairness of the SAT.

## Understanding the data

We are using data on the SAT scores of high schoolers, along with other
data sets relating to demographics and other information.

Let’s read each data file into a df

``` r
# Get the list of files to read in
files <- list.files(path = "data-in", pattern = "*.csv", full.names = TRUE)

# Read in all files to a list of df
# The setNames function will apply the filename as the name of each element
# in the list
df_list <- setNames(lapply(files, read_csv), files)
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   DBN = col_character(),
    ##   SchoolName = col_character(),
    ##   `AP Test Takers` = col_double(),
    ##   `Total Exams Taken` = col_double(),
    ##   `Number of Exams with scores 3 4 or 5` = col_double()
    ## )

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   CSD = col_double(),
    ##   BOROUGH = col_character(),
    ##   `SCHOOL CODE` = col_character(),
    ##   `SCHOOL NAME` = col_character(),
    ##   GRADE = col_character(),
    ##   `PROGRAM TYPE` = col_character(),
    ##   `CORE SUBJECT (MS CORE and 9-12 ONLY)` = col_character(),
    ##   `CORE COURSE (MS CORE and 9-12 ONLY)` = col_character(),
    ##   `SERVICE CATEGORY(K-9* ONLY)` = col_character(),
    ##   `NUMBER OF STUDENTS / SEATS FILLED` = col_double(),
    ##   `NUMBER OF SECTIONS` = col_double(),
    ##   `AVERAGE CLASS SIZE` = col_double(),
    ##   `SIZE OF SMALLEST CLASS` = col_double(),
    ##   `SIZE OF LARGEST CLASS` = col_double(),
    ##   `DATA SOURCE` = col_character(),
    ##   `SCHOOLWIDE PUPIL-TEACHER RATIO` = col_double()
    ## )

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   .default = col_double(),
    ##   DBN = col_character(),
    ##   Name = col_character()
    ## )
    ## ℹ Use `spec()` for the full column specifications.

    ## Warning: 6 parsing failures.
    ##  row        col expected actual                       file
    ## 1124 fl_percent a double    n/a 'data-in/demographics.csv'
    ## 2154 fl_percent a double    n/a 'data-in/demographics.csv'
    ## 2161 fl_percent a double    n/a 'data-in/demographics.csv'
    ## 4299 fl_percent a double    n/a 'data-in/demographics.csv'
    ## 8523 fl_percent a double    n/a 'data-in/demographics.csv'
    ## .... .......... ........ ...... ..........................
    ## See problems(...) for more details.

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   the_geom = col_character(),
    ##   SchoolDist = col_double(),
    ##   Shape_Area = col_double(),
    ##   Shape_Leng = col_double()
    ## )

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   .default = col_double(),
    ##   Demographic = col_character(),
    ##   DBN = col_character(),
    ##   `School Name` = col_character(),
    ##   Cohort = col_character(),
    ##   `Total Grads - n` = col_character(),
    ##   `Total Regents - n` = col_character(),
    ##   `Advanced Regents - n` = col_character(),
    ##   `Regents w/o Advanced - n` = col_character(),
    ##   `Local - n` = col_character(),
    ##   `Still Enrolled - n` = col_character(),
    ##   `Dropped Out - n` = col_character()
    ## )
    ## ℹ Use `spec()` for the full column specifications.

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   DBN = col_character(),
    ##   Grade = col_character(),
    ##   Year = col_double(),
    ##   Category = col_character(),
    ##   `Number Tested` = col_double(),
    ##   `Mean Scale Score` = col_double(),
    ##   `Level 1 #` = col_double(),
    ##   `Level 1 %` = col_double(),
    ##   `Level 2 #` = col_double(),
    ##   `Level 2 %` = col_double(),
    ##   `Level 3 #` = col_double(),
    ##   `Level 3 %` = col_double(),
    ##   `Level 4 #` = col_double(),
    ##   `Level 4 %` = col_double(),
    ##   `Level 3+4 #` = col_double(),
    ##   `Level 3+4 %` = col_double()
    ## )

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   DBN = col_character(),
    ##   `SCHOOL NAME` = col_character(),
    ##   `Num of SAT Test Takers` = col_character(),
    ##   `SAT Critical Reading Avg. Score` = col_character(),
    ##   `SAT Math Avg. Score` = col_character(),
    ##   `SAT Writing Avg. Score` = col_character()
    ## )

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   District = col_character(),
    ##   `YTD % Attendance (Avg)` = col_double(),
    ##   `YTD Enrollment(Avg)` = col_double()
    ## )

``` r
# Create a variable for each df
ap_results <- df_list[[1]]
class_size <- df_list[[2]]
demographics <- df_list[[3]]
district_maps <- df_list[[4]]
grad_outcomes <- df_list[[5]]
math_results <- df_list[[6]]
sat_scores <- df_list[[7]]
school_attendance <- df_list[[8]]
```

Once we have read in the data, we can use the `head` function to view
the first 6 rows of data in each df.

``` r
lapply(df_list, head)
```

    ## $`data-in/ap_results.csv`
    ## # A tibble: 6 x 5
    ##   DBN    SchoolName       `AP Test Takers` `Total Exams Ta… `Number of Exams wi…
    ##   <chr>  <chr>                       <dbl>            <dbl>                <dbl>
    ## 1 01M448 UNIVERSITY NEIG…               39               49                   10
    ## 2 01M450 EAST SIDE COMMU…               19               21                   NA
    ## 3 01M515 LOWER EASTSIDE …               24               26                   24
    ## 4 01M539 NEW EXPLORATION…              255              377                  191
    ## 5 02M296 High School of …               NA               NA                   NA
    ## 6 02M298 Pace High School               21               21                   NA
    ## 
    ## $`data-in/class_size.csv`
    ## # A tibble: 6 x 16
    ##     CSD BOROUGH `SCHOOL CODE` `SCHOOL NAME`             GRADE `PROGRAM TYPE`
    ##   <dbl> <chr>   <chr>         <chr>                     <chr> <chr>         
    ## 1     1 M       M015          P.S. 015 Roberto Clemente 0K    GEN ED        
    ## 2     1 M       M015          P.S. 015 Roberto Clemente 0K    CTT           
    ## 3     1 M       M015          P.S. 015 Roberto Clemente 01    GEN ED        
    ## 4     1 M       M015          P.S. 015 Roberto Clemente 01    CTT           
    ## 5     1 M       M015          P.S. 015 Roberto Clemente 02    GEN ED        
    ## 6     1 M       M015          P.S. 015 Roberto Clemente 02    CTT           
    ## # … with 10 more variables: CORE SUBJECT (MS CORE and 9-12 ONLY) <chr>,
    ## #   CORE COURSE (MS CORE and 9-12 ONLY) <chr>,
    ## #   SERVICE CATEGORY(K-9* ONLY) <chr>, NUMBER OF STUDENTS / SEATS FILLED <dbl>,
    ## #   NUMBER OF SECTIONS <dbl>, AVERAGE CLASS SIZE <dbl>,
    ## #   SIZE OF SMALLEST CLASS <dbl>, SIZE OF LARGEST CLASS <dbl>,
    ## #   DATA SOURCE <chr>, SCHOOLWIDE PUPIL-TEACHER RATIO <dbl>
    ## 
    ## $`data-in/demographics.csv`
    ## # A tibble: 6 x 38
    ##   DBN    Name     schoolyear fl_percent frl_percent total_enrollment  prek     k
    ##   <chr>  <chr>         <dbl>      <dbl>       <dbl>            <dbl> <dbl> <dbl>
    ## 1 01M015 P.S. 01…   20052006       89.4        NA                281    15    36
    ## 2 01M015 P.S. 01…   20062007       89.4        NA                243    15    29
    ## 3 01M015 P.S. 01…   20072008       89.4        NA                261    18    43
    ## 4 01M015 P.S. 01…   20082009       89.4        NA                252    17    37
    ## 5 01M015 P.S. 01…   20092010       NA          96.5              208    16    40
    ## 6 01M015 P.S. 01…   20102011       NA          96.5              203    13    37
    ## # … with 30 more variables: grade1 <dbl>, grade2 <dbl>, grade3 <dbl>,
    ## #   grade4 <dbl>, grade5 <dbl>, grade6 <dbl>, grade7 <dbl>, grade8 <dbl>,
    ## #   grade9 <dbl>, grade10 <dbl>, grade11 <dbl>, grade12 <dbl>, ell_num <dbl>,
    ## #   ell_percent <dbl>, sped_num <dbl>, sped_percent <dbl>, ctt_num <dbl>,
    ## #   selfcontained_num <dbl>, asian_num <dbl>, asian_per <dbl>, black_num <dbl>,
    ## #   black_per <dbl>, hispanic_num <dbl>, hispanic_per <dbl>, white_num <dbl>,
    ## #   white_per <dbl>, male_num <dbl>, male_per <dbl>, female_num <dbl>,
    ## #   female_per <dbl>
    ## 
    ## $`data-in/district_maps.csv`
    ## # A tibble: 6 x 4
    ##   the_geom                                      SchoolDist Shape_Area Shape_Leng
    ##   <chr>                                              <dbl>      <dbl>      <dbl>
    ## 1 MULTIPOLYGON (((-73.97177411031375 40.725821…          1  35160328.     28641.
    ## 2 MULTIPOLYGON (((-73.86789798628837 40.902940…         10 282540990.     94957.
    ## 3 MULTIPOLYGON (((-73.78833349900695 40.834667…         11 393227696.    305036.
    ## 4 MULTIPOLYGON (((-73.93311862859143 40.695791…         16  46763620.     35849.
    ## 5 MULTIPOLYGON (((-73.88284445574813 40.847817…         12  69097946.     48578.
    ## 6 MULTIPOLYGON (((-73.9790608491185 40.7059460…         13 104870780.     86635.
    ## 
    ## $`data-in/grad_outcomes.csv`
    ## # A tibble: 6 x 23
    ##   Demographic  DBN    `School Name`       Cohort `Total Cohort` `Total Grads - …
    ##   <chr>        <chr>  <chr>               <chr>           <dbl> <chr>           
    ## 1 Total Cohort 01M292 HENRY STREET SCHOO… 2003                5 s               
    ## 2 Total Cohort 01M292 HENRY STREET SCHOO… 2004               55 37              
    ## 3 Total Cohort 01M292 HENRY STREET SCHOO… 2005               64 43              
    ## 4 Total Cohort 01M292 HENRY STREET SCHOO… 2006               78 43              
    ## 5 Total Cohort 01M292 HENRY STREET SCHOO… 2006 …             78 44              
    ## 6 Total Cohort 01M448 UNIVERSITY NEIGHBO… 2001               64 46              
    ## # … with 17 more variables: Total Grads - % of cohort <dbl>,
    ## #   Total Regents - n <chr>, Total Regents - % of cohort <dbl>,
    ## #   Total Regents - % of grads <dbl>, Advanced Regents - n <chr>,
    ## #   Advanced Regents - % of cohort <dbl>, Advanced Regents - % of grads <dbl>,
    ## #   Regents w/o Advanced - n <chr>, Regents w/o Advanced - % of cohort <dbl>,
    ## #   Regents w/o Advanced - % of grads <dbl>, Local - n <chr>,
    ## #   Local - % of cohort <dbl>, Local - % of grads <dbl>,
    ## #   Still Enrolled - n <chr>, Still Enrolled - % of cohort <dbl>,
    ## #   Dropped Out - n <chr>, Dropped Out - % of cohort <dbl>
    ## 
    ## $`data-in/math_results.csv`
    ## # A tibble: 6 x 16
    ##   DBN    Grade  Year Category     `Number Tested` `Mean Scale Score` `Level 1 #`
    ##   <chr>  <chr> <dbl> <chr>                  <dbl>              <dbl>       <dbl>
    ## 1 01M015 3      2006 All Students              39                667           2
    ## 2 01M015 3      2007 All Students              31                672           2
    ## 3 01M015 3      2008 All Students              37                668           0
    ## 4 01M015 3      2009 All Students              33                668           0
    ## 5 01M015 3      2010 All Students              26                677           6
    ## 6 01M015 3      2011 All Students              28                671          10
    ## # … with 9 more variables: Level 1 % <dbl>, Level 2 # <dbl>, Level 2 % <dbl>,
    ## #   Level 3 # <dbl>, Level 3 % <dbl>, Level 4 # <dbl>, Level 4 % <dbl>,
    ## #   Level 3+4 # <dbl>, Level 3+4 % <dbl>
    ## 
    ## $`data-in/sat_scores.csv`
    ## # A tibble: 6 x 6
    ##   DBN    `SCHOOL NAME`     `Num of SAT Test… `SAT Critical Rea… `SAT Math Avg. …
    ##   <chr>  <chr>             <chr>             <chr>              <chr>           
    ## 1 01M292 HENRY STREET SCH… 29                355                404             
    ## 2 01M448 UNIVERSITY NEIGH… 91                383                423             
    ## 3 01M450 EAST SIDE COMMUN… 70                377                402             
    ## 4 01M458 FORSYTH SATELLIT… 7                 414                401             
    ## 5 01M509 MARTA VALLE HIGH… 44                390                433             
    ## 6 01M515 LOWER EAST SIDE … 112               332                557             
    ## # … with 1 more variable: SAT Writing Avg. Score <chr>
    ## 
    ## $`data-in/school_attendance.csv`
    ## # A tibble: 6 x 3
    ##   District    `YTD % Attendance (Avg)` `YTD Enrollment(Avg)`
    ##   <chr>                          <dbl>                 <dbl>
    ## 1 DISTRICT 01                     91.2                 12367
    ## 2 DISTRICT 02                     89.0                 60823
    ## 3 DISTRICT 03                     89.3                 21962
    ## 4 DISTRICT 04                     91.1                 14252
    ## 5 DISTRICT 05                     89.1                 13170
    ## 6 DISTRICT 06                     91.3                 25733
