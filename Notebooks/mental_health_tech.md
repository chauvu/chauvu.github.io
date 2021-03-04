OSMI Mental Health Survey by Tech Workers
================

This project clean and explores the 2014 OSMI mental health on workers in the tech field on Kaggle [link](https://www.kaggle.com/osmi/mental-health-in-tech-survey). This is the raw dataset, so I will aggregate the questions and answers regarding mental health and perform exploratory data analysis.

### Load the OSMI dataset

``` r
library(readr)
library(tidyverse)
library(ggplot2)

data <- read_csv('../Data/survey.csv')
```

### Variable `data` preview

``` r
data %>% head()
```

    ## # A tibble: 6 x 27
    ##   Timestamp             Age Gender Country    state self_employed family_history
    ##   <dttm>              <dbl> <chr>  <chr>      <chr> <chr>         <chr>         
    ## 1 2014-08-27 11:29:31    37 Female United St… IL    <NA>          No            
    ## 2 2014-08-27 11:29:37    44 M      United St… IN    <NA>          No            
    ## 3 2014-08-27 11:29:44    32 Male   Canada     <NA>  <NA>          No            
    ## 4 2014-08-27 11:29:46    31 Male   United Ki… <NA>  <NA>          Yes           
    ## 5 2014-08-27 11:30:22    31 Male   United St… TX    <NA>          No            
    ## 6 2014-08-27 11:31:22    33 Male   United St… TN    <NA>          Yes           
    ## # … with 20 more variables: treatment <chr>, work_interfere <chr>,
    ## #   no_employees <chr>, remote_work <chr>, tech_company <chr>, benefits <chr>,
    ## #   care_options <chr>, wellness_program <chr>, seek_help <chr>,
    ## #   anonymity <chr>, leave <chr>, mental_health_consequence <chr>,
    ## #   phys_health_consequence <chr>, coworkers <chr>, supervisor <chr>,
    ## #   mental_health_interview <chr>, phys_health_interview <chr>,
    ## #   mental_vs_physical <chr>, obs_consequence <chr>, comments <chr>

``` r
data %>% colnames()
```

    ##  [1] "Timestamp"                 "Age"                      
    ##  [3] "Gender"                    "Country"                  
    ##  [5] "state"                     "self_employed"            
    ##  [7] "family_history"            "treatment"                
    ##  [9] "work_interfere"            "no_employees"             
    ## [11] "remote_work"               "tech_company"             
    ## [13] "benefits"                  "care_options"             
    ## [15] "wellness_program"          "seek_help"                
    ## [17] "anonymity"                 "leave"                    
    ## [19] "mental_health_consequence" "phys_health_consequence"  
    ## [21] "coworkers"                 "supervisor"               
    ## [23] "mental_health_interview"   "phys_health_interview"    
    ## [25] "mental_vs_physical"        "obs_consequence"          
    ## [27] "comments"

This dataset seems overwhelming at first, especially because there are so many columns available. I will start off by removing columns `Timestamp`, `Country`, `state`, and `comments`.

``` r
data <- data %>% select(-Timestamp, -Country, -state, -comments)
```

### `Age`

`Age` has no missing values, but the minimum age is `-1726` and maximum age is `1e+11`. We will restrict the age range and set any values outside this range to NaN; after this, we now have 5 NaN values in the Age column. Sine the histogram is fairly normally-distributed, we will use mean-imputation to fill in missing values.

``` r
data$Age %>% is.na() %>% sum()
```

    ## [1] 0

``` r
min(data$Age)
```

    ## [1] -1726

``` r
max(data$Age)
```

    ## [1] 1e+11

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
data$Age %>% is.na() %>% sum()
```

    ## [1] 5

``` r
data$Age[is.na(data$Age)] <- mean(data$Age[!is.na(data$Age)])
```

### `Gender`

By looking at the unique values in this `Gender` column, this variable is quite heterogeneous. Apart from the binary Male and Female classification, there are also non-binary LGBTQ+ answers. It is further complicated by many misspellings and vague answers, such as `a little about you`. We will tackle this variable through the following steps:

1.  Change any ambiguous/undeciperable answers to NaN.
2.  Group non-binary gender identifications in `nonbinary` category.
3.  Correct for all misspellings.

At the risk of offending the audience, since there are many non-binary categories a person can identify as (the LGBTQ acronym requires a + at the end to address other terms), I will group all non-binary gender identifications as `nonbinary`. Since the dataset only contains ~1000 rows, further subdividing this category is detrimental to our analysis.

``` r
data$Gender <- tolower(data$Gender)
data$Gender %>% unique
```

    ##  [1] "female"                                        
    ##  [2] "m"                                             
    ##  [3] "male"                                          
    ##  [4] "male-ish"                                      
    ##  [5] "maile"                                         
    ##  [6] "trans-female"                                  
    ##  [7] "cis female"                                    
    ##  [8] "f"                                             
    ##  [9] "something kinda male?"                         
    ## [10] "cis male"                                      
    ## [11] "woman"                                         
    ## [12] "mal"                                           
    ## [13] "male (cis)"                                    
    ## [14] "queer/she/they"                                
    ## [15] "non-binary"                                    
    ## [16] "femake"                                        
    ## [17] "make"                                          
    ## [18] "nah"                                           
    ## [19] "all"                                           
    ## [20] "enby"                                          
    ## [21] "fluid"                                         
    ## [22] "genderqueer"                                   
    ## [23] "androgyne"                                     
    ## [24] "agender"                                       
    ## [25] "cis-female/femme"                              
    ## [26] "guy (-ish) ^_^"                                
    ## [27] "male leaning androgynous"                      
    ## [28] "man"                                           
    ## [29] "trans woman"                                   
    ## [30] "msle"                                          
    ## [31] "neuter"                                        
    ## [32] "female (trans)"                                
    ## [33] "queer"                                         
    ## [34] "female (cis)"                                  
    ## [35] "mail"                                          
    ## [36] "a little about you"                            
    ## [37] "malr"                                          
    ## [38] "p"                                             
    ## [39] "femail"                                        
    ## [40] "cis man"                                       
    ## [41] "ostensibly male, unsure what that really means"

*Step 1*: I have identified the following answers as undecipherable: `nah`, `all`, `a little about you`. The answer `nah` could mean the person invokes their right not to provide an answer. The answer `all` could possible stand for `pansexual`, but since I cannot confirm this I will classify it as a missing answer.

``` r
data$Gender[data$Gender == 'nah' | data$Gender == 'all' | data$Gender=='a little about you'] = NaN
```

*Step 2*: I will group non-binary gender identifications into `nonbinary` category. I will do this iteratively to make I do not exclude any possible answers. First, any answers that contains words `trans`, `queer`, `enby` (NB=nonbinary), `andro` (androgynous), `fluid` are converted.

``` r
data$Gender <- map_chr(data$Gender, function(x) if_else(any(str_detect(x, c('trans', 'queer', 'enby', 'andro', 'fluid'))), 'nonbinary', x))
data$Gender %>% unique()
```

    ##  [1] "female"                                        
    ##  [2] "m"                                             
    ##  [3] "male"                                          
    ##  [4] "male-ish"                                      
    ##  [5] "maile"                                         
    ##  [6] "nonbinary"                                     
    ##  [7] "cis female"                                    
    ##  [8] "f"                                             
    ##  [9] "something kinda male?"                         
    ## [10] "cis male"                                      
    ## [11] "woman"                                         
    ## [12] "mal"                                           
    ## [13] "male (cis)"                                    
    ## [14] "non-binary"                                    
    ## [15] "femake"                                        
    ## [16] "make"                                          
    ## [17] "NaN"                                           
    ## [18] "agender"                                       
    ## [19] "cis-female/femme"                              
    ## [20] "guy (-ish) ^_^"                                
    ## [21] "man"                                           
    ## [22] "msle"                                          
    ## [23] "neuter"                                        
    ## [24] "female (cis)"                                  
    ## [25] "mail"                                          
    ## [26] "malr"                                          
    ## [27] "p"                                             
    ## [28] "femail"                                        
    ## [29] "cis man"                                       
    ## [30] "ostensibly male, unsure what that really means"

After this, I see that the unique values have decreased quite a bit. There are a few more categories that I will classify as non-binary, including `p` (pansexual), `neuter`, `agender`, `something kinda male?` and `ostensibly male, unsure what that really means`. The last two are quite confusing, likely because the responders are confused themselves. Since these two answers are quite specific, they likely represent only 2 responders (out of ~1000), so classify them as non-binary will not make a difference on our analysis.

``` r
data$Gender <- map_chr(data$Gender, function(x) if_else(any(str_detect(x, c('p', 'neuter', 'agender', 'something kinda male?', 'ostensibly male, unsure what that really means'))), 'nonbinary', x))
```

*Step 3*: correct for ALL misspellings of `male` and `female`. After converting all misspellings of `female`, other values are `male`.

``` r
data$Gender[str_detect(data$Gender, 'fe')] <- 'female'
data$Gender[str_detect(data$Gender, 'wo')] <- 'female'
data$Gender[data$Gender=='f'] <- 'female'

data$Gender[data$Gender != 'female' & data$Gender != 'nonbinary' & data$Gender != 'NaN'] <- 'male'
```

After cleaning up the data, let's visualize the frequency plot for each category. Is the result quite so surprising, that the majority of responders are male? Since the tech industry is dominated by heterosexual males, this result is not unusual. Additionally, sine the mode of this feature is `male`, we will fill in the NaN values as male. ![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-13-1.png)

``` r
data$Gender[data$Gender == 'NaN'] <- 'male'
```

### Self Employment

Variable `self_employed` should be a binary variable, with `Yes` or `No`. There are a total of 18 missing values; looking at the frequency plot of the answers, the mode is `No`, so I will fill the missing values as `No`. This makes sense because most tech jobs are located at big tech companies, so only a small amount of workers are self-employed.

``` r
data$self_employed %>% unique()
```

    ## [1] NA    "Yes" "No"

``` r
data$self_employed %>% is.na() %>% sum()
```

    ## [1] 18

``` r
data %>% ggplot(aes(self_employed)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-16-1.png)

``` r
data$self_employed[is.na(data$self_employed)] <- 'No'
```

### Family History, Treatment, Remote Work

The variables `family_history` (whether the responder has a family history of mental conditions), `treatment` (whether responder has sought mental treatments), `remote_work` have no missing values and contains binary answers `Yes` and `No`. Therefore, I will not modify this column.

``` r
data$family_history %>% unique()
```

    ## [1] "No"  "Yes"

``` r
data$family_history %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data$treatment %>% unique()
```

    ## [1] "Yes" "No"

``` r
data$treatment %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data$remote_work %>% unique()
```

    ## [1] "No"  "Yes"

``` r
data$remote_work %>% is.na() %>% sum()
```

    ## [1] 0

### Number of employees

The variable `no_employees` represents the responder's estimates of company size. Since this column is a vector of **characters**, we will look at unique answers. It looks like the employees provide a range of how many people are at their company; interesting, self-employed people mostly work at companies of 1-5 size, whereas other workers tend to work at larger companies.

``` r
typeof(data$no_employees)
```

    ## [1] "character"

``` r
data$no_employees %>% unique()
```

    ## [1] "6-25"           "More than 1000" "26-100"         "100-500"       
    ## [5] "1-5"            "500-1000"

``` r
data$no_employees %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(no_employees)) + geom_bar() + facet_wrap(~ self_employed)
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-19-1.png)

Since there are quite a number of categories available for this column, I will categorize the company size into 3 distinct groups:

-   `Large`: &gt; 500 employees
-   `Medium`: 100 - 500 employees
-   `Small`: &lt; 25 employees

``` r
data$no_employees[data$no_employees=='1-5' | data$no_employees=='6-25'] = 'Small'
data$no_employees[data$no_employees=='500-1000' | data$no_employees=='More than 1000'] = 'Large'
data$no_employees[data$no_employees!='Small' & data$no_employees!='Large'] = 'Medium'
```

We will also `factor` (dummify) the `no_employees` column with the correct order small &lt; medium &lt; large.

``` r
data$no_employees <- factor(data$no_employees, levels=c('Small', 'Medium', 'Large'))
```

### Tech Company

Variable `tech_company` answers whether the responder works at a tech company and has no missing values. Even though this survey is targeted at employees at tech corporations, there are also tech workers who work at primarily non-tech companies (e.g. software developer for finance company). However, since the answer `Yes` heavily overwhelms `No`, this feature will not provide a lot of information for analysis. Therefore, I will drop this column.

``` r
data$tech_company %>% unique()
```

    ## [1] "Yes" "No"

``` r
data$tech_company %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(tech_company)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-22-1.png)

``` r
data <- data %>% select(-tech_company)
```

### Benefits

The `benefits` variable has no missing value and has 3 unique answers `Yes`, `No` and `Don't know`. If the `Don't know` category is small compared to the remaining two categories, I could have designated it as NaN and impute using the mode. However, as shown in the frequency plot, the `Don't know` category takes up approximately a third of the entries, so I will leave the categories as is.

``` r
data$benefits %>% unique()
```

    ## [1] "Yes"        "Don't know" "No"

``` r
data$benefits %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(benefits)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-23-1.png)

### Care Options

Similarly, `care_options` (whether company provide mental health care options) also has fairly equal distribution between 3 options `Yes`, `No` and `Don't know`. Even though the `Don't know` responders are likely in the `No` category (in my personal opinion, if a company has mental health program but fails to market it to employees, then the program is ineffective and nonexistent), I will not modify this column and leave the 3 answers as is.

``` r
data$care_options %>% unique()
```

    ## [1] "Not sure" "No"       "Yes"

``` r
data$care_options %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(care_options)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-24-1.png)

### Wellness Program

On the other hand, the variable `wellness_program` has 3 options and no missing variable but the distribution is not even between the three different categories. The `No` responders heavily outweighs the `Yes` and `Don't know`. Therefore, I will also drop this column.

``` r
data$wellness_program %>% unique()
```

    ## [1] "No"         "Don't know" "Yes"

``` r
data$wellness_program %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(wellness_program)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-25-1.png)

``` r
data <- data %>% select(-wellness_program)
```

### Seek Help

The variable `seek_help` answers whether the employer provides resources/opportunities for workers to seek mental health. Since there are no missing values and the distribution is not extremely uneven between the three options, I will leave this column as is.

``` r
data$seek_help %>% unique()
```

    ## [1] "Yes"        "Don't know" "No"

``` r
data$seek_help %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(seek_help)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-26-1.png)

### Anonymity

Similar to previous columns, this `anonymity` column (whether anonymity is protected) is heavily unevenly distributed, so I will drop it.

``` r
data$anonymity %>% unique()
```

    ## [1] "Yes"        "Don't know" "No"

``` r
data$anonymity %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(anonymity)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-27-1.png)

``` r
data <- data %>% select(-anonymity)
```

### Leave

The variable `leave` represents the availability of medical leave for mental health conditions. This column is also very unbalanced, so I will drop it.

``` r
data$leave %>% unique()
```

    ## [1] "Somewhat easy"      "Don't know"         "Somewhat difficult"
    ## [4] "Very difficult"     "Very easy"

``` r
data$leave %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(leave)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-28-1.png)

``` r
data <- data %>% select(-leave)
```

### Mental and Physical Health Consequence

The `mental_health_consequence` and `phys_health_consequence` (consequences if employer knows about mental or physical health conditions respectively) both do not have missing values. The `mental_health_consequence` column is evenly distributed, but the `phys_health_consequence` is not. If `phys_health_consequence` is a standalone independent column, I would drop it; however, since its value is a direct comparison with `mental_health_consequence`, I will leave it in.

``` r
data$mental_health_consequence %>% unique()
```

    ## [1] "No"    "Maybe" "Yes"

``` r
data$mental_health_consequence %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(mental_health_consequence)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-29-1.png)

``` r
data$phys_health_consequence %>% unique()
```

    ## [1] "No"    "Yes"   "Maybe"

``` r
data$phys_health_consequence %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(phys_health_consequence)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-29-2.png)

Let's combine these two categories into a `health_consequence` categories.

``` r
data <- data %>% mutate(
  health_consequence = str_c(mental_health_consequence, ' ', phys_health_consequence)
)
data %>% ggplot(aes(health_consequence)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-30-1.png)

There should be 9 categories (3x3), however we only see 8 categories. This is because the `No Yes` (no for mental, yes for physical) is nonexistent. Having 8 categories still makes this analysis quite complex. However, the goal of this is to compare between mental and physical health consequence, so I will group into

-   `Neither`: `No No`, `No Maybe`
-   `Both`: `Yes Yes`, `Maybe Maybe`, `Maybe Yes`
-   `Mental`: `Yes No`, `Maybe No`, `Yes Maybe`

``` r
data$health_consequence[data$health_consequence=='No No' | data$health_consequence=='No Maybe'] = 'Neither'
data$health_consequence[data$health_consequence=='Yes Yes' | data$health_consequence=='Maybe Maybe' | data$health_consequence=='Maybe Yes'] = 'Both'
data$health_consequence[data$health_consequence=='Yes No' | data$health_consequence=='Maybe No' | data$health_consequence=='Yes Maybe'] = 'Mental'

data %>% ggplot(aes(health_consequence)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-31-1.png)

### Coworkers and Supervisor

Variables `coworkers` and `supervisor` (willingness to discuss mental health condition with coworkers and supervisors respectively) are related; therefore, even though `coworkers` is unbalanced (as expected since people are more likely to discuss personal issues with coworkers) I will keep both.

``` r
data$coworkers %>% unique()
```

    ## [1] "Some of them" "No"           "Yes"

``` r
data$coworkers %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(coworkers)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-32-1.png)

``` r
data$supervisor %>% unique()
```

    ## [1] "Yes"          "No"           "Some of them"

``` r
data$supervisor %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(supervisor)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-32-2.png)

Let's combine into `coworker_supervisor`:

``` r
data <- data %>% mutate(
  coworker_supervisor = str_c(coworkers, ' ', supervisor)
)
data %>% ggplot(aes(coworker_supervisor)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-33-1.png)

There are 9 categories. Let's try splitting into these 4 categories:

-   `Neither`: `No No`
-   `Both`: `Yes Yes`, `Yes Some`, `Some Yes`, `Some Some`
-   `Coworker`: `Yes No`, `Some No`
-   `Supervisor`: `No Yes`, `No Some`

``` r
data$coworker_supervisor[data$coworker_supervisor=='No No'] = 'Neither'
data$coworker_supervisor[data$coworker_supervisor=='Yes Yes' | data$coworker_supervisor=='Yes Some of them' | data$coworker_supervisor=='Some of them Yes' | data$coworker_supervisor=='Some of them Some of them'] = 'Both'
data$coworker_supervisor[data$coworker_supervisor=='Yes No' | data$coworker_supervisor=='Some of them No'] = 'Coworker'
data$coworker_supervisor[data$coworker_supervisor=='No Yes' | data$coworker_supervisor=='No Some of them'] = 'Supervisor'

data %>% ggplot(aes(coworker_supervisor)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-34-1.png)

### Mental/Physical Health in Interview

Variables `mental_health_interview` and `phys_health_interview` (willingness to bring up mental or physical health conditions at interviews) are related and left as is.

``` r
data$mental_health_interview %>% unique()
```

    ## [1] "No"    "Yes"   "Maybe"

``` r
data$mental_health_interview %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(mental_health_interview)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-35-1.png)

``` r
data$phys_health_interview %>% unique()
```

    ## [1] "Maybe" "No"    "Yes"

``` r
data$phys_health_interview %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(phys_health_interview)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-35-2.png)

Let's try a different approach for this category. We can rank the answers as `No=0`, `Maybe=1` and `Yes=2` (degree of willingness). Afterward, we create column `phys_mental_interview` as the difference between willingness to bring up physical condition compared to mental condition.

``` r
data <- data %>% mutate(
  mental_interview_rank = case_when(
    mental_health_interview == 'Yes' ~ 2,
    mental_health_interview == 'Maybe' ~ 1,
    TRUE ~ 0
  ),
  phys_interview_rank = case_when(
    phys_health_interview == 'Yes' ~ 2,
    phys_health_interview == 'Maybe' ~ 1,
    TRUE ~ 0
  ),
  phys_mental_interview = phys_interview_rank - mental_interview_rank
)
data <- data %>% select(-mental_interview_rank, -phys_interview_rank)

data %>% ggplot(aes(phys_mental_interview)) + geom_histogram(bins=5)
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-36-1.png)

Since we see this is a very skewed histogram, the majority of answers imply that there is no difference between willingness to bring up mental and physical conditions during interview. There is also a portion of responders who are less willing to bring up mental compared to physical health conditions (`phys_mental_interview` &gt; 0). Therefore, I will create `phys_more_mental_interview` as a binary variable:

-   `Yes`: `phys_mental_interview` &gt; 0
-   `No`: `phys_mental_interview` &lt;= 0

``` r
data <- data %>% mutate(
  phys_more_mental_interview = if_else(phys_mental_interview > 0, 'Yes', 'No')
)
data <- data %>% select(-phys_mental_interview)
data %>% ggplot(aes(phys_more_mental_interview)) + geom_histogram(stat='count')
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-37-1.png)

### Mental vs. Physical

Variable `mental_vs_physical` (whether employer considers mental health issues as important as physical health issues) is evenly distributed and has no missing values.

``` r
data$mental_vs_physical %>% unique()
```

    ## [1] "Yes"        "Don't know" "No"

``` r
data$mental_vs_physical %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(mental_vs_physical)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-38-1.png)

### Observed consequences

Variable `obs_consequence` is extremely unbalanced, with most responders answering `No`. Therefore, I will drop this column.

``` r
data$obs_consequence %>% unique()
```

    ## [1] "No"  "Yes"

``` r
data$obs_consequence %>% is.na() %>% sum()
```

    ## [1] 0

``` r
data %>% ggplot(aes(obs_consequence)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-39-1.png)

``` r
data <- data %>% select(-obs_consequence)
```

### Work Interference

The variable `work_interfere` (whether work interferes with mental health) is a multiple choice answer, with 4 possible answers `Often`, `Sometimes`, `Rarely` and `Never`.

There are a total of 264 missing values, which represents also a quarter of the dataset. Therefore, we will have to consider whether it is okay to impute for this column or drop it entirely.

``` r
data$work_interfere %>% unique()
```

    ## [1] "Often"     "Rarely"    "Never"     "Sometimes" NA

``` r
data$work_interfere %>% is.na() %>% sum()
```

    ## [1] 264

Interestingly, people who do not answer have primarily replied `No` to `treatment` (whether sought treatment for mental conditions). It makes sense because if these workers do not suffer from mental health conditions, they would not know if work interferes with mental health.

Looking at the distribution in the **no-treatment** group, I see that most people answer `Never` to work interference. On the other hand, in the **yes-treatment** group, there are only 4 NA rows, and most responders reply `Sometimes`. Therefore, I will impute the missing values using the mode within the `treatment` subgroups.

``` r
data %>% filter(treatment=='Yes', is.na(work_interfere)) %>% nrow()
```

    ## [1] 4

``` r
data %>% ggplot(aes(work_interfere)) + geom_bar() + facet_wrap(~ treatment)
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-41-1.png)

``` r
data$work_interfere[is.na(data$work_interfere) & data$treatment=='No'] = 'Never'
data$work_interfere[is.na(data$work_interfere) & data$treatment=='Yes'] = 'Sometimes'
```

Let's look at the `work_interfere` distribution again. Most values fall in the `Never` and `Sometimes` category, whether only a small portion of responses are `Rarely` or `Often`. To reduce the number of categories in this feature and still retain the balanced distribution of categories, I will turn this into a binary column:

-   `More`: includes `Often` and `Sometimes` responses
-   `Less`: includes `Never` and `Rarely` responses

``` r
data %>% ggplot(aes(work_interfere)) + geom_bar()
```

![](mental_health_tech_files/figure-markdown_github/unnamed-chunk-43-1.png)

``` r
data$work_interfere[data$work_interfere=='Often'|data$work_interfere=='Sometimes'] = 'More'
data$work_interfere[data$work_interfere=='Never'|data$work_interfere=='Rarely'] = 'Less'
```

Summary
-------

Overall, we have cleaned the data. All the missing values have been filled in, and columns that are not useful for analysis have been removed. I have also added some columns based on related variables in the dataset, mostly comparative variables between how employers treat mental vs. physical health conditions. This project illustrates data cleaning and exploration in R code. Even though the dataset is quite small, analysis can still yield quite interesting insights and represents a potential future project. And since this raw dataset is only for the 2014 OSMI survey, analysis with additional OSMI survey from 2010-2020 can yield more information on the perception and prevalence of mental health conditions in the tech industry.

Additionally, the author has not undergone rigorous training regarding issues such as and not limited to mental health and gender identities. I have learnt a lot through processing this dataset and look forward to researching and learning more about these pressing social issues.
