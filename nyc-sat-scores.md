NYC SAT Scores
================
Clare Gibson
2021-05-13

# Setup

First we will load the libraries we need for this analysis.

``` r
library(tidyverse)
library(janitor)
library(corrplot)
```

# Scope

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

# Understanding the data

We are using data on the SAT scores of high schoolers, along with other
data sets relating to demographics and other information.

Let’s read each data file into a df.

``` r
# Get the list of files to read in
files <- list.files(path = "data-in", pattern = "*.csv", full.names = TRUE)

# Extract the data set names from the filenames. We can use these to name
# elements in our list of df
names <- str_extract(files, "(?<=data-in\\/)(.+)(?=\\.csv)")

# Read in all files to a list of df
# The setNames function will apply the filename as the name of each element
# in the list
data <- setNames(lapply(files, read_csv), names)
```

Once we have read in the data, we can use the `names` function to see
the column names in each df.

``` r
lapply(data, names)
```

    ## $ap_results
    ## [1] "DBN"                                 
    ## [2] "SchoolName"                          
    ## [3] "AP Test Takers"                      
    ## [4] "Total Exams Taken"                   
    ## [5] "Number of Exams with scores 3 4 or 5"
    ## 
    ## $class_size
    ##  [1] "CSD"                                 
    ##  [2] "BOROUGH"                             
    ##  [3] "SCHOOL CODE"                         
    ##  [4] "SCHOOL NAME"                         
    ##  [5] "GRADE"                               
    ##  [6] "PROGRAM TYPE"                        
    ##  [7] "CORE SUBJECT (MS CORE and 9-12 ONLY)"
    ##  [8] "CORE COURSE (MS CORE and 9-12 ONLY)" 
    ##  [9] "SERVICE CATEGORY(K-9* ONLY)"         
    ## [10] "NUMBER OF STUDENTS / SEATS FILLED"   
    ## [11] "NUMBER OF SECTIONS"                  
    ## [12] "AVERAGE CLASS SIZE"                  
    ## [13] "SIZE OF SMALLEST CLASS"              
    ## [14] "SIZE OF LARGEST CLASS"               
    ## [15] "DATA SOURCE"                         
    ## [16] "SCHOOLWIDE PUPIL-TEACHER RATIO"      
    ## 
    ## $demographics
    ##  [1] "DBN"               "Name"              "schoolyear"       
    ##  [4] "fl_percent"        "frl_percent"       "total_enrollment" 
    ##  [7] "prek"              "k"                 "grade1"           
    ## [10] "grade2"            "grade3"            "grade4"           
    ## [13] "grade5"            "grade6"            "grade7"           
    ## [16] "grade8"            "grade9"            "grade10"          
    ## [19] "grade11"           "grade12"           "ell_num"          
    ## [22] "ell_percent"       "sped_num"          "sped_percent"     
    ## [25] "ctt_num"           "selfcontained_num" "asian_num"        
    ## [28] "asian_per"         "black_num"         "black_per"        
    ## [31] "hispanic_num"      "hispanic_per"      "white_num"        
    ## [34] "white_per"         "male_num"          "male_per"         
    ## [37] "female_num"        "female_per"       
    ## 
    ## $grad_outcomes
    ##  [1] "Demographic"                        "DBN"                               
    ##  [3] "School Name"                        "Cohort"                            
    ##  [5] "Total Cohort"                       "Total Grads - n"                   
    ##  [7] "Total Grads - % of cohort"          "Total Regents - n"                 
    ##  [9] "Total Regents - % of cohort"        "Total Regents - % of grads"        
    ## [11] "Advanced Regents - n"               "Advanced Regents - % of cohort"    
    ## [13] "Advanced Regents - % of grads"      "Regents w/o Advanced - n"          
    ## [15] "Regents w/o Advanced - % of cohort" "Regents w/o Advanced - % of grads" 
    ## [17] "Local - n"                          "Local - % of cohort"               
    ## [19] "Local - % of grads"                 "Still Enrolled - n"                
    ## [21] "Still Enrolled - % of cohort"       "Dropped Out - n"                   
    ## [23] "Dropped Out - % of cohort"         
    ## 
    ## $hs_directory
    ##  [1] "dbn"                              "school_name"                     
    ##  [3] "borough"                          "building_code"                   
    ##  [5] "phone_number"                     "fax_number"                      
    ##  [7] "grade_span_min"                   "grade_span_max"                  
    ##  [9] "expgrade_span_min"                "expgrade_span_max"               
    ## [11] "bus"                              "subway"                          
    ## [13] "primary_address_line_1"           "city"                            
    ## [15] "state_code"                       "postcode"                        
    ## [17] "website"                          "total_students"                  
    ## [19] "campus_name"                      "school_type"                     
    ## [21] "overview_paragraph"               "program_highlights"              
    ## [23] "language_classes"                 "advancedplacement_courses"       
    ## [25] "online_ap_courses"                "online_language_courses"         
    ## [27] "extracurricular_activities"       "psal_sports_boys"                
    ## [29] "psal_sports_girls"                "psal_sports_coed"                
    ## [31] "school_sports"                    "partner_cbo"                     
    ## [33] "partner_hospital"                 "partner_highered"                
    ## [35] "partner_cultural"                 "partner_nonprofit"               
    ## [37] "partner_corporate"                "partner_financial"               
    ## [39] "partner_other"                    "addtl_info1"                     
    ## [41] "addtl_info2"                      "start_time"                      
    ## [43] "end_time"                         "se_services"                     
    ## [45] "ell_programs"                     "school_accessibility_description"
    ## [47] "number_programs"                  "priority01"                      
    ## [49] "priority02"                       "priority03"                      
    ## [51] "priority04"                       "priority05"                      
    ## [53] "priority06"                       "priority07"                      
    ## [55] "priority08"                       "priority09"                      
    ## [57] "priority10"                       "Location 1"                      
    ## [59] "Community Board"                  "Council District"                
    ## [61] "Census Tract"                     "BIN"                             
    ## [63] "BBL"                              "NTA"                             
    ## 
    ## $math_results
    ##  [1] "DBN"              "Grade"            "Year"             "Category"        
    ##  [5] "Number Tested"    "Mean Scale Score" "Level 1 #"        "Level 1 %"       
    ##  [9] "Level 2 #"        "Level 2 %"        "Level 3 #"        "Level 3 %"       
    ## [13] "Level 4 #"        "Level 4 %"        "Level 3+4 #"      "Level 3+4 %"     
    ## 
    ## $sat_scores
    ## [1] "DBN"                             "SCHOOL NAME"                    
    ## [3] "Num of SAT Test Takers"          "SAT Critical Reading Avg. Score"
    ## [5] "SAT Math Avg. Score"             "SAT Writing Avg. Score"

Some items of interest from this initial peek:

-   Many of the column headings are capitalized and contain spaces. To
    make things easier in R, I will need to clean them.
-   Most of the data sets contain a `DBN` column which appears to be a
    unique identifier for the school.
-   Some of the data sets contain records for more than one year, others
    are for a single year.
-   Some of the columns may need further cleaning (e.g. percentages may
    need to be divided by 100)

## Cleaning the column headings

The `janitor` package has a handy function called `clean_names()` that
will ensure column names are unique and consist only of the `_`
character, numbers and lowercase letters.

``` r
data <- 
  lapply(data, clean_names)
```

# Unifying the data

Our next step is to combine the different data sets in to one large
dataframe, so that we can compare columns across data sets. It looks
like we may be able to use `dbn` as a joining column.

However, some data sets do not have a `dbn` column:

-   `class_size`: it looks like we can construct the `dbn` by using a
    combination of `csd`, `borough` and `school_code`.
-   `district_maps` and `school_attendance`: these data sets appear to
    be aggregated at the school district level rather than the
    individual school level.

## Adding `dbn` to `class_size`

Let’s take a look at `dbn` in one of the other data sets where it
appears.

``` r
head(data$sat_scores$dbn)
```

    ## [1] "01M292" "01M448" "01M450" "01M458" "01M509" "01M515"

As [this
article](https://teachnyc.zendesk.com/hc/en-us/articles/360053601831-What-is-a-DBN-District-Borough-Number- "NYC DBN")
explains, the DBN or District Borough Number is the combination of
District Number, the letter code for the borough and the number of the
school.

![](https://teachnyc.zendesk.com/hc/article_attachments/360079392731/Screen_Shot_2020-12-10_at_10.55.35_AM.png)

Let’s look again at the column headings we have in the `class_size` df.

``` r
names(data$class_size)
```

    ##  [1] "csd"                                "borough"                           
    ##  [3] "school_code"                        "school_name"                       
    ##  [5] "grade"                              "program_type"                      
    ##  [7] "core_subject_ms_core_and_9_12_only" "core_course_ms_core_and_9_12_only" 
    ##  [9] "service_category_k_9_only"          "number_of_students_seats_filled"   
    ## [11] "number_of_sections"                 "average_class_size"                
    ## [13] "size_of_smallest_class"             "size_of_largest_class"             
    ## [15] "data_source"                        "schoolwide_pupil_teacher_ratio"

From the [data
dictionary](https://data.cityofnewyork.us/api/views/urz7-pzb3/files/73f3bc71-a52a-4a80-b496-ab2a43b8021f?download=true&filename=Class_Size_Open_Data_Dictionary.xlsx "Class size data dictionary")
that accompanies this data set on the [NYC Open
Data](https://opendata.cityofnewyork.us/ "NYC Open Data") site, I know
that `csd` is the Community School District number, `borough` is the
borough code and `school_code` is the school code.

Let’s have a look at how these columns look in the `class_size` df.

``` r
data$class_size %>% 
  select(csd, borough, school_code) %>% 
  head()
```

    ## # A tibble: 6 x 3
    ##     csd borough school_code
    ##   <dbl> <chr>   <chr>      
    ## 1     1 M       M015       
    ## 2     1 M       M015       
    ## 3     1 M       M015       
    ## 4     1 M       M015       
    ## 5     1 M       M015       
    ## 6     1 M       M015

So it looks like in this case the `school_code` column is already a
concatenation of the borough code and the school code. We simply need to
add the district number, with a leading 0.

``` r
data$class_size <- 
  data$class_size %>% 
  mutate(dbn = paste0(str_pad(csd, 2, side = "left", pad = "0"), school_code)) %>% 
  select(dbn, everything()) # puts the new dbn column at the front

head(data$class_size$dbn)
```

    ## [1] "01M015" "01M015" "01M015" "01M015" "01M015" "01M015"

## Adding in the surveys

We also have some data relating to student, parent and teacher surveys
about the quality of schools. These surveys include information about
the perceived safety of each school, academic standards and more. We
will need to add in this data before we can combine the data sets. The
survey data is located in two `.txt` files in the `data-in` directory.
These are tab-delimited files and we can read them in using
`read_tsv()`.

``` r
# Get the list of files to read in 
survey_files <- list.files(path = "data-in", pattern = "*.txt", full.names = TRUE)

# Extract the data set names from the filenames. We can use these to name
# elements in our list of df
survey_names <- str_extract(survey_files, "(?<=data-in\\/)(.+)(?=\\.txt)")

# Read in all files to a list of survey data
survey_data <- setNames(lapply(survey_files, read_tsv), survey_names)
```

There are actually far too many columns in the survey data to be useful
for our analysis. We can resolve this issue by looking at the [data
dictionary](https://data.cityofnewyork.us/api/views/mnz3-dyi8/files/aa68d821-4dbb-4eb2-9448-3d8cbbad5044?download=true&filename=Survey%20Data%20Dictionary.xls "Survey data dictionary")
file that accompanies this data set on the [NYC Open
Data](https://opendata.cityofnewyork.us/ "NYC Open Data") site. We’ll
use the list of fields in the first section of the data dictionary for
our analysis.

![](images/survey-data-dictionary.png)

``` r
# First we'll select the columns we need from the survey_all data
survey_data$survey_all <-
  survey_data$survey_all %>% 
  select(dbn:aca_tot_11)

# Then we'll select the columns we need from the survey_d75 data
survey_data$survey_d75 <- 
  survey_data$survey_d75 %>% 
  select(dbn:aca_tot_11)
```

Now it looks like both of these df have the same column headings, so we
can bind them into a single df and append that df to the main data list.

``` r
# Bind the two survey df into one df and clean the names
survey_data <- survey_data %>% 
  bind_rows() %>% 
  clean_names()

# Append the survey data to the data list
data <- c(data, survey = list(survey_data))
```

## Condensing the data sets

If we take a look at some of the data sets, including `class_size` we’ll
immiediately see a problem.

``` r
head(data$class_size)
```

    ## # A tibble: 6 x 17
    ##   dbn      csd borough school_code school_name               grade program_type
    ##   <chr>  <dbl> <chr>   <chr>       <chr>                     <chr> <chr>       
    ## 1 01M015     1 M       M015        P.S. 015 Roberto Clemente 0K    GEN ED      
    ## 2 01M015     1 M       M015        P.S. 015 Roberto Clemente 0K    CTT         
    ## 3 01M015     1 M       M015        P.S. 015 Roberto Clemente 01    GEN ED      
    ## 4 01M015     1 M       M015        P.S. 015 Roberto Clemente 01    CTT         
    ## 5 01M015     1 M       M015        P.S. 015 Roberto Clemente 02    GEN ED      
    ## 6 01M015     1 M       M015        P.S. 015 Roberto Clemente 02    CTT         
    ## # … with 10 more variables: core_subject_ms_core_and_9_12_only <chr>,
    ## #   core_course_ms_core_and_9_12_only <chr>, service_category_k_9_only <chr>,
    ## #   number_of_students_seats_filled <dbl>, number_of_sections <dbl>,
    ## #   average_class_size <dbl>, size_of_smallest_class <dbl>,
    ## #   size_of_largest_class <dbl>, data_source <chr>,
    ## #   schoolwide_pupil_teacher_ratio <dbl>

There are several rows for each high school (as you can see by the
repeated `school_code` field). In order to combine this data set with
the SAT scores data, we need to be able to condense data sets like
`class size` to the point where there is just one row per school. In the
`class_size` data set it looks like `grade` and `program_type` have
multiple values per school. By restricting each field to a single value,
we can filter most of the duplicate rows. Let’s filter `class_size` as
follows:

-   Only select records where `grade` is `09-12`
-   Only select records where `program_type` is `GEN ED`
-   Group the `class_size` data set by `dbn`, and take the average of
    each column. Essentially we will find the average class size for
    each school.

``` r
data$class_size <- data$class_size %>% 
  filter(grade == "09-12",
         program_type == "GEN ED") %>% 
  select(dbn, where(is.numeric)) %>% 
  group_by(dbn) %>% 
  summarise(across(everything(), mean))
```

Let’s check that this has removed all of the duplicate `dbn` in the
`class_size` data set.

``` r
anyDuplicated(data$class_size$dbn)
```

    ## [1] 0

Some of the other data sets also need to be condensed. In the
`demographics` data set, data was collected for multiple years for the
same school. Let’s filter to include only the most recent year for each
school. We can then run the same check for any remaining duplicated
`dbn`.

``` r
data$demographics <- data$demographics %>% 
  filter(schoolyear == "20112012")

anyDuplicated(data$demographics$dbn)
```

    ## [1] 0

In the `math_results` data set, schools are further segmented by `grade`
and `year`. We can select a single grade and a single year.

``` r
data$math_results <- data$math_results %>% 
  filter(year == 2011,
         grade == "8")

anyDuplicated(data$math_results$dbn)
```

    ## [1] 0

Finally, `grad_outcomes` needs to be condensed.

``` r
data$grad_outcomes <- data$grad_outcomes %>% 
  filter(cohort == "2006",
         demographic == "Total Cohort")

anyDuplicated(data$grad_outcomes$dbn)
```

    ## [1] 0

We have checked a few of our data sets for duplicated `dbn` but we
haven’t checked all of them. Let’s write a function to check everything.

``` r
duplicate_check <- 
  function(df) {
    anyDuplicated(df$dbn)
  }

lapply(data, duplicate_check)
```

    ## $ap_results
    ## [1] 53
    ## 
    ## $class_size
    ## [1] 0
    ## 
    ## $demographics
    ## [1] 0
    ## 
    ## $grad_outcomes
    ## [1] 0
    ## 
    ## $hs_directory
    ## [1] 0
    ## 
    ## $math_results
    ## [1] 0
    ## 
    ## $sat_scores
    ## [1] 0
    ## 
    ## $survey
    ## [1] 0

Look’s like something funny is going on with `ap_results`. Let’s take a
closer look.

``` r
data$ap_results %>% 
  group_by(dbn) %>% 
  mutate(dbn_count = n()) %>% 
  filter(dbn_count > 1) %>% 
  print()
```

    ## # A tibble: 2 x 6
    ## # Groups:   dbn [1]
    ##   dbn    school_name  ap_test_takers total_exams_tak… number_of_exams… dbn_count
    ##   <chr>  <chr>                 <dbl>            <dbl>            <dbl>     <int>
    ## 1 04M610 THE YOUNG W…             41               55               29         2
    ## 2 04M610 YOUNG WOMEN…             NA               NA               NA         2

It looks like this school has been entered twice. One of the rows has no
data so we can remove this row.

``` r
data$ap_results <- data$ap_results %>% 
  filter(school_name != "YOUNG WOMEN'S LEADERSHIP SCH")

anyDuplicated(data$ap_results$dbn)
```

    ## [1] 0

# Computing variables

Computing variables can help speed up our analysis by enabling us to
make comparisons more quickly. We can compute a total SAT score from the
individual columns `sat_critical_reading_avg_score`,
`sat_math_avg_score` and `sat_writing_avg_score`. Before we can do this
we will need to convert those columns from string to number.

``` r
data$sat_scores <- data$sat_scores %>% 
  mutate(across(num_of_sat_test_takers:sat_writing_avg_score, as.numeric)) %>% 
  mutate(sat_score = sat_critical_reading_avg_score +
                     sat_math_avg_score +
                     sat_writing_avg_score)

head(data$sat_scores)
```

    ## # A tibble: 6 x 7
    ##   dbn    school_name       num_of_sat_test_… sat_critical_read… sat_math_avg_sc…
    ##   <chr>  <chr>                         <dbl>              <dbl>            <dbl>
    ## 1 01M292 HENRY STREET SCH…                29                355              404
    ## 2 01M448 UNIVERSITY NEIGH…                91                383              423
    ## 3 01M450 EAST SIDE COMMUN…                70                377              402
    ## 4 01M458 FORSYTH SATELLIT…                 7                414              401
    ## 5 01M509 MARTA VALLE HIGH…                44                390              433
    ## 6 01M515 LOWER EAST SIDE …               112                332              557
    ## # … with 2 more variables: sat_writing_avg_score <dbl>, sat_score <dbl>

Next, let’s parse out the coordinate locations of each school, so we can
make maps. This will enable us to plot the location of each school.
Let’s take a look at the `location_1` column in `hs_directory` where
these coordinates are stored

``` r
head(data$hs_directory$location_1)
```

    ## [1] "8 21 Bay 25 Street\nFar Rockaway, NY 11691\n(40.601989336, -73.762834323)"
    ## [2] "2630 Benson Avenue\nBrooklyn, NY 11214\n(40.593593811, -73.984729232)"    
    ## [3] "1014 Lafayette Avenue\nBrooklyn, NY 11221\n(40.692133704, -73.931503172)" 
    ## [4] "1980 Lafayette Avenue\nBronx, NY 10473\n(40.822303765, -73.85596139)"     
    ## [5] "100 Amsterdam Avenue\nNew York, NY 10023\n(40.773670507, -73.985268558)"  
    ## [6] "350 Grand Street\nNew York, NY 10002\n(40.716867224, -73.989531943)"

The coordinates all appear to follow a newline character (`\n`) and an
opening parenthesis (`(`).

``` r
data$hs_directory <- data$hs_directory %>% 
  mutate(lat = as.numeric(str_extract(location_1, "(?<=\\\n\\()(-*\\d+\\.\\d+)")),
         lon = as.numeric(str_extract(location_1, "(-*\\d+\\.\\d+)(?=\\)$)")))

data$hs_directory %>% 
  select(lat, lon) %>% 
  head()
```

    ## # A tibble: 6 x 2
    ##     lat   lon
    ##   <dbl> <dbl>
    ## 1  40.6 -73.8
    ## 2  40.6 -74.0
    ## 3  40.7 -73.9
    ## 4  40.8 -73.9
    ## 5  40.8 -74.0
    ## 6  40.7 -74.0

# Combining the data sets

Now we are ready to combine our data sets into one large dataframe using
the `dbn` column. We will need to perform a `full_join` to ensure that
we don’t lose any data, since not all schools are present in all data
sets.

``` r
full <- data$hs_directory %>% 
  full_join(data$ap_results,
            by = "dbn") %>% 
  full_join(data$class_size,
            by = "dbn") %>% 
  full_join(data$demographics,
            by = "dbn") %>% 
  full_join(data$grad_outcomes,
            by = "dbn") %>% 
  full_join(data$math_results,
            by = "dbn") %>%
  full_join(data$sat_scores,
            by = "dbn") %>% 
  full_join(data$survey,
            by = "dbn")

head(full)
```

    ## # A tibble: 6 x 188
    ##   dbn    school_name.x             borough building_code phone_number fax_number
    ##   <chr>  <chr>                     <chr>   <chr>         <chr>        <chr>     
    ## 1 27Q260 Frederick Douglass Acade… Queens  Q465          718-471-2154 718-471-2…
    ## 2 21K559 Life Academy High School… Brookl… K400          718-333-7750 718-333-7…
    ## 3 16K393 Frederick Douglass Acade… Brookl… K026          718-574-2820 718-574-2…
    ## 4 08X305 Pablo Neruda Academy      Bronx   X450          718-824-1682 718-824-1…
    ## 5 03M485 Fiorello H. LaGuardia Hi… Manhat… M485          212-496-0700 212-724-5…
    ## 6 02M305 Urban Assembly Academy o… Manhat… M445          212-505-0745 212-674-8…
    ## # … with 182 more variables: grade_span_min <dbl>, grade_span_max <dbl>,
    ## #   expgrade_span_min <dbl>, expgrade_span_max <dbl>, bus <chr>, subway <chr>,
    ## #   primary_address_line_1 <chr>, city <chr>, state_code <chr>, postcode <dbl>,
    ## #   website <chr>, total_students <dbl>, campus_name <chr>, school_type <chr>,
    ## #   overview_paragraph <chr>, program_highlights <chr>, language_classes <chr>,
    ## #   advancedplacement_courses <chr>, online_ap_courses <chr>,
    ## #   online_language_courses <chr>, extracurricular_activities <chr>,
    ## #   psal_sports_boys <chr>, psal_sports_girls <chr>, psal_sports_coed <chr>,
    ## #   school_sports <chr>, partner_cbo <chr>, partner_hospital <chr>,
    ## #   partner_highered <chr>, partner_cultural <chr>, partner_nonprofit <chr>,
    ## #   partner_corporate <chr>, partner_financial <chr>, partner_other <chr>,
    ## #   addtl_info1 <chr>, addtl_info2 <chr>, start_time <time>, end_time <time>,
    ## #   se_services <chr>, ell_programs <chr>,
    ## #   school_accessibility_description <chr>, number_programs <dbl>,
    ## #   priority01 <chr>, priority02 <chr>, priority03 <chr>, priority04 <chr>,
    ## #   priority05 <chr>, priority06 <chr>, priority07 <chr>, priority08 <chr>,
    ## #   priority09 <chr>, priority10 <chr>, location_1 <chr>,
    ## #   community_board <dbl>, council_district <dbl>, census_tract <dbl>,
    ## #   bin <dbl>, bbl <dbl>, nta <chr>, lat <dbl>, lon <dbl>, school_name.y <chr>,
    ## #   ap_test_takers <dbl>, total_exams_taken <dbl>,
    ## #   number_of_exams_with_scores_3_4_or_5 <dbl>, csd <dbl>,
    ## #   number_of_students_seats_filled <dbl>, number_of_sections <dbl>,
    ## #   average_class_size <dbl>, size_of_smallest_class <dbl>,
    ## #   size_of_largest_class <dbl>, schoolwide_pupil_teacher_ratio <dbl>,
    ## #   name <chr>, schoolyear <dbl>, fl_percent <dbl>, frl_percent <dbl>,
    ## #   total_enrollment <dbl>, prek <dbl>, k <dbl>, grade1 <dbl>, grade2 <dbl>,
    ## #   grade3 <dbl>, grade4 <dbl>, grade5 <dbl>, grade6 <dbl>, grade7 <dbl>,
    ## #   grade8 <dbl>, grade9 <dbl>, grade10 <dbl>, grade11 <dbl>, grade12 <dbl>,
    ## #   ell_num <dbl>, ell_percent <dbl>, sped_num <dbl>, sped_percent <dbl>,
    ## #   ctt_num <dbl>, selfcontained_num <dbl>, asian_num <dbl>, asian_per <dbl>,
    ## #   black_num <dbl>, black_per <dbl>, …

# Adding in columns

Now that we have our `full` data frame we are almost ready to do our
analysis. We first need to check for missing data. Some of the AP scores
data has missing values. We can convert these to 0.

``` r
full <- full %>% 
  replace_na(list(ap_test_takers = 0,
                  total_exams_taken = 0,
                  number_of_exams_with_scores_3_4_or_5 = 0))

full %>% 
  select(c(ap_test_takers, total_exams_taken, number_of_exams_with_scores_3_4_or_5)) %>% 
  head()
```

    ## # A tibble: 6 x 3
    ##   ap_test_takers total_exams_taken number_of_exams_with_scores_3_4_or_5
    ##            <dbl>             <dbl>                                <dbl>
    ## 1              0                 0                                    0
    ## 2              0                 0                                    0
    ## 3              0                 0                                    0
    ## 4              0                 0                                    0
    ## 5            691              1236                                  790
    ## 6             25                37                                   15

Next we need to add a `school_dist` column that indicates the school
district of the school. this will enable us to match up school districts
and plot out district level statistics using the district maps we
downloaded earlier. Recalling our [earlier
image](https://teachnyc.zendesk.com/hc/article_attachments/360079392731/Screen_Shot_2020-12-10_at_10.55.35_AM.png "DBN Explainer"),
this can be extracted from the `dbn` column by taking the first 2
characters from the DBN code.

``` r
full <- full %>% 
  mutate(school_dist = str_sub(dbn,1,2))

head(full$school_dist)
```

    ## [1] "27" "21" "16" "08" "03" "02"

# Computing correlations

A good way to explore a data set and see what columns are related to the
one you care about is to compute correlations. In this case we are
interested in the `sat_score` column.

In order to do this we will need to impute any missing values in the
numerical columns. We will simply use the column mean as our imputed
value.

``` r
# Select only numeric columns
full_num <- full %>% 
  select(where(is.numeric))

# Impute missing values
#full_num <- full_num %>% 
#  mutate(across(everything(),
#                ~ if_else(is.na(.x), mean(.x, na.rm = TRUE), .x)))

# Create a dataframe of correlation coefficients (r)
# Every variable is correlated against every other
full_cor <- as_tibble(cor(full_num, use = "pairwise.complete.obs", method = "pearson"),
                      rownames = "variable")

# Select just the sat_score correlations
sat_cor <- full_cor %>% 
  select(variable, sat_score)
```

One [widely accepted
interpretation](http://www.statstutor.ac.uk/resources/uploaded/pearsons.pdf "Interpreting correlation coefficients")
of correlation coefficients states that anything with an absolute
coefficient of 0.4 or above can be considered to be moderately
correlated.

Let’s just show the variables that are correlated moderately or above
with `sat_score`. We can remove `sat_score` from the variable column
(since this just shows how `sat_score` is correlated with itself) along
with the columns that factored into the `sat_score` calculation as we
know they will also be correlated.

``` r
sat_cor %>% 
  filter(!variable %in% c("sat_score",
                          "sat_writing_avg_score",
                          "sat_critical_reading_avg_score",
                          "sat_math_avg_score"),
         sat_score >= 0.4 | sat_score <= -0.4) %>% 
  arrange(desc(sat_score))
```

    ## # A tibble: 39 x 2
    ##    variable                           sat_score
    ##    <chr>                                  <dbl>
    ##  1 grade1                                 1    
    ##  2 grade2                                 1    
    ##  3 grade3                                 1    
    ##  4 grade4                                 1    
    ##  5 grade5                                 1    
    ##  6 k                                      0.985
    ##  7 mean_scale_score                       0.791
    ##  8 advanced_regents_percent_of_cohort     0.782
    ##  9 level_3_4_percent                      0.771
    ## 10 advanced_regents_percent_of_grads      0.743
    ## # … with 29 more rows

Given that one of our initial research questions was to explore any
relationships between race and SAT scores, let’s also do a deeper dive
into some of the demographic variables. We can plot a few of these on
scatter plots alongside SAT score.

Let’s define a function to draw our scatter plots

``` r
scatter <- function(x,y) {
  plot(x, y, pch = 19, col = "lightblue")
  abline(lm(y ~ x), col = "red", lwd = 3)
  text(paste("Correlation:", round(cor(x, y, use = "complete.obs"), 2)), x = 60, y = 1000)
}
```

Now let’s plot percent of white students against SAT score.
![](nyc-sat-scores_files/figure-gfm/scatter%20white-1.png)<!-- -->

Percent of Asian students against SAT acores.
![](nyc-sat-scores_files/figure-gfm/scatter%20asian-1.png)<!-- -->

Percent of hispanic students against SAT score.
![](nyc-sat-scores_files/figure-gfm/scatter%20hispanic-1.png)<!-- -->

And percent of black students.
![](nyc-sat-scores_files/figure-gfm/scatter%20black-1.png)<!-- -->
