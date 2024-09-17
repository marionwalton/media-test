---
title: Text as data (Optional)
teaching: 30
exercises: 15
source: Rmd
---



:::: instructor

- This is an optional lesson intended to introduce approaches to text as data and 
  key concepts from natural language processing, as well as how to
  apply computational approaches to qualitatively oriented discourse analysis.
- Note that his lesson was community-contributed and remains a work in progress. As such, it could
  benefit from feedback from instructors and/or workshop participants.

::::::::::::

::::::::::::::::::::::::::::::::::::::: objectives

- Describe the JSON data format
- Understand where JSON is typically used
- Appreciate some advantages of using JSON over tabular data
- Appreciate some disadvantages of processing JSON documents
- Use the jsonLite package to read a JSON file
- Display formatted JSON as dataframe
- Select and display nested dataframe fields from a JSON document
- Write tabular data from selected elements from a JSON document to a csv file

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- What is JSON format?
- How can I convert JSON to an R dataframe?
- How can I convert an array of JSON record into a table?

::::::::::::::::::::::::::::::::::::::::::::::::::

## The JSON data format

The JSON data format was designed as a way of allowing different machines or processes within machines to communicate with each other by sending messages constructed in a well defined format. JSON is now the preferred data format used by APIs (Application Programming Interfaces).

The JSON format although somewhat verbose is not only Human readable but it can also be mapped very easily to an R dataframe.

We are going to read a file of data formatted as JSON, convert it into a dataframe in R then selectively create a csv file from the extracted data.

The JSON file we are going to use is the [SAFI.json](data/SAFI.json) file. This is the output file from an electronic survey system called ODK. The JSON represents the answers to a series of survey questions. The questions themselves have been replaced with unique Keys, the values are the answers.

Because detailed surveys are by nature nested structures making it possible to record different levels of detail or selectively ask a set of specific questions based on the answer given to a previous question, the structure of the answers for the survey can not only be complex and convoluted, it could easily be different from one survey respondent's set of answers to another.

### Advantages of JSON

- Very popular data format for APIs (e.g. results from an Internet search)
- Human readable
- Each record (or document as they are called) is self contained. The equivalent of the column name and column values are in every record.
- Documents do not all have to have the same structure within the same file
- Document structures can be complex and nested

### Disadvantages of JSON

- It is more verbose than the equivalent data in csv format
- Can be more difficult to process and display than csv formatted data

## Use the JSON package to read a JSON file


``` r
library(jsonlite)
```

As with reading in a CSV, you have a couple of options for how to access the JSON file.

You can read the JSON file directly into R with `read_json()` or the comparable `fromJSON()`
function, though this does not download the file.


``` r
json_data <- read_json(
  "https://raw.githubusercontent.com/datacarpentry/r-socialsci/main/episodes/data/SAFI.json"
  )
```

To download the file you can copy and paste the contents of the file on
[GitHub](https://github.com/datacarpentry/r-socialsci/blob/main/episodes/data/SAFI.json),
creating a `SAFI.json` file in your `data` directory, or you can download the file with R.


``` r
download.file(
  "https://raw.githubusercontent.com/datacarpentry/r-socialsci/main/episodes/data/SAFI.json",
   "data/SAFI.json", mode = "wb")
```

Once you have the data downloaded, you can read it into R with `read_json()`:


``` r
json_data <- read_json("data/SAFI.json")
```

We can see that a new object called json\_data has appeared in our Environment. It is described as a Large list (131 elements). In this current form, our data is messy. You can have a glimpse of it with the `head()` or `view()` functions. It will look not much more structured than if you were to open the JSON file with a text editor.

This is because, by default, the `read_json()` function's parameter `simplifyVector`, which specifies whether or not to simplify vectors is set to FALSE. This means that the default setting does not simplify nested lists into vectors and data frames. However, we can set this to TRUE, and our data will be read directly as a dataframe:


``` r
json_data <- read_json("data/SAFI.json", simplifyVector = TRUE)
```

Now we can see we have this json data in a dataframe format. For consistency with the rest of
the lesson, let's coerce it to be a tibble and use `glimpse` to take a peek
inside (these functions were loaded by `library(tidyverse)`):


``` r
json_data <- json_data %>% as_tibble()
glimpse(json_data)
```

``` output
Rows: 131
Columns: 74
$ C06_rooms                      <int> 1, 1, 1, 1, 1, 1, 1, 3, 1, 5, 1, 3, 1, …
$ B19_grand_liv                  <chr> "no", "yes", "no", "no", "yes", "no", "…
$ A08_ward                       <chr> "ward2", "ward2", "ward2", "ward2", "wa…
$ E01_water_use                  <chr> "no", "yes", "no", "no", "no", "no", "y…
$ B18_sp_parents_liv             <chr> "yes", "yes", "no", "no", "no", "no", "…
$ B16_years_liv                  <int> 4, 9, 15, 6, 40, 3, 38, 70, 6, 23, 20, …
$ E_yes_group_count              <chr> NA, "3", NA, NA, NA, NA, "4", "2", "3",…
$ F_liv                          <list> [<data.frame[1 x 2]>], [<data.frame[3 …
$ `_note2`                       <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ instanceID                     <chr> "uuid:ec241f2c-0609-46ed-b5e8-fe575f6ce…
$ B20_sp_grand_liv               <chr> "yes", "yes", "no", "no", "no", "no", "…
$ F10_liv_owned_other            <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ `_note1`                       <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ F12_poultry                    <chr> "yes", "yes", "yes", "yes", "yes", "no"…
$ D_plots_count                  <chr> "2", "3", "1", "3", "2", "1", "4", "2",…
$ C02_respondent_wall_type_other <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ C02_respondent_wall_type       <chr> "muddaub", "muddaub", "burntbricks", "b…
$ C05_buildings_in_compound      <int> 1, 1, 1, 1, 1, 1, 1, 2, 2, 1, 2, 2, 1, …
$ `_remitters`                   <list> [<data.frame[0 x 0]>], [<data.frame[0 …
$ E18_months_no_water            <list> <NULL>, <"Aug", "Sept">, <NULL>, <NULL…
$ F07_use_income                 <chr> NA, "AlimentaÃ§Ã£o e pagamento de educa…
$ G01_no_meals                   <int> 2, 2, 2, 2, 2, 2, 3, 2, 3, 3, 2, 3, 2, …
$ E17_no_enough_water            <chr> NA, "yes", NA, NA, NA, NA, "yes", "yes"…
$ F04_need_money                 <chr> NA, "no", NA, NA, NA, NA, "no", "no", "…
$ A05_end                        <chr> "2017-04-02T17:29:08.000Z", "2017-04-02…
$ C04_window_type                <chr> "no", "no", "yes", "no", "no", "no", "n…
$ E21_other_meth                 <chr> NA, "no", NA, NA, NA, NA, "no", "no", "…
$ D_no_plots                     <int> 2, 3, 1, 3, 2, 1, 4, 2, 3, 2, 2, 2, 4, …
$ F05_money_source               <list> <NULL>, <NULL>, <NULL>, <NULL>, <NULL>…
$ A07_district                   <chr> "district1", "district1", "district1", …
$ C03_respondent_floor_type      <chr> "earth", "earth", "cement", "earth", "e…
$ E_yes_group                    <list> [<data.frame[0 x 0]>], [<data.frame[3 …
$ A01_interview_date             <chr> "2016-11-17", "2016-11-17", "2016-11-17…
$ B11_remittance_money           <chr> "no", "no", "no", "no", "no", "no", "no…
$ A04_start                      <chr> "2017-03-23T09:49:57.000Z", "2017-04-02…
$ D_plots                        <list> [<data.frame[2 x 8]>], [<data.frame[3 …
$ F_items                        <list> [<data.frame[3 x 3]>], [<data.frame[2 …
$ F_liv_count                    <chr> "1", "3", "1", "2", "4", "1", "1", "2",…
$ F10_liv_owned                  <list> "poultry", <"oxen", "cows", "goats">, …
$ B_no_membrs                    <int> 3, 7, 10, 7, 7, 3, 6, 12, 8, 12, 6, 7, …
$ F13_du_look_aftr_cows          <chr> "no", "no", "no", "no", "no", "no", "no…
$ E26_affect_conflicts           <chr> NA, "once", NA, NA, NA, NA, "never", "n…
$ F14_items_owned                <list> <"bicycle", "television", "solar_panel…
$ F06_crops_contr                <chr> NA, "more_half", NA, NA, NA, NA, "more_…
$ B17_parents_liv                <chr> "no", "yes", "no", "no", "yes", "no", "…
$ G02_months_lack_food           <list> "Jan", <"Jan", "Sept", "Oct", "Nov", "…
$ A11_years_farm                 <dbl> 11, 2, 40, 6, 18, 3, 20, 16, 16, 22, 6,…
$ F09_du_labour                  <chr> "no", "no", "yes", "yes", "no", "yes", …
$ E_no_group_count               <chr> "2", NA, "1", "3", "2", "1", NA, NA, NA…
$ E22_res_change                 <list> <NULL>, <NULL>, <NULL>, <NULL>, <NULL>…
$ E24_resp_assoc                 <chr> NA, "no", NA, NA, NA, NA, NA, "yes", NA…
$ A03_quest_no                   <chr> "01", "01", "03", "04", "05", "6", "7",…
$ `_members`                     <list> [<data.frame[3 x 12]>], [<data.frame[7…
$ A06_province                   <chr> "province1", "province1", "province1", …
$ `gps:Accuracy`                 <dbl> 14, 19, 13, 5, 10, 12, 11, 9, 11, 14, 1…
$ E20_exper_other                <chr> NA, "yes", NA, NA, NA, NA, "yes", "yes"…
$ A09_village                    <chr> "village2", "village2", "village2", "vi…
$ C01_respondent_roof_type       <chr> "grass", "grass", "mabatisloping", "mab…
$ `gps:Altitude`                 <dbl> 698, 690, 674, 679, 689, 692, 709, 700,…
$ `gps:Longitude`                <dbl> 33.48346, 33.48342, 33.48345, 33.48342,…
$ E23_memb_assoc                 <chr> NA, "yes", NA, NA, NA, NA, "no", "yes",…
$ E19_period_use                 <dbl> NA, 2, NA, NA, NA, NA, 10, 10, 6, 22, N…
$ E25_fees_water                 <chr> NA, "no", NA, NA, NA, NA, "no", "no", "…
$ C07_other_buildings            <chr> "no", "no", "no", "no", "no", "no", "ye…
$ observation                    <chr> "None", "Estes primeiros inquÃ©ritos na…
$ `_note`                        <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ A12_agr_assoc                  <chr> "no", "yes", "no", "no", "no", "no", "n…
$ G03_no_food_mitigation         <list> <"na", "rely_less_food", "reduce_meals…
$ F05_money_source_other         <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ `gps:Latitude`                 <dbl> -19.11226, -19.11248, -19.11211, -19.11…
$ E_no_group                     <list> [<data.frame[2 x 6]>], [<data.frame[0 …
$ F14_items_owned_other          <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ F08_emply_lab                  <chr> "no", "yes", "no", "no", "no", "no", "n…
$ `_members_count`               <chr> "3", "7", "10", "7", "7", "3", "6", "12…
```

Looking good, but you might notice that actually we have a variable, *F\_liv* that is a list of dataframes! It is very important to know what you are expecting from your data to be able to look for things like this. For example, if you are getting your JSON from an API, have a look at the API documentation, so you know what to look for.

Often when we have a very large number of columns, it can become difficult to determine all the variables which may require some special attention, like lists. Fortunately, we can use special verbs like `where` to quickly select all the list columns.


``` r
json_data %>%
    select(where(is.list)) %>%
    glimpse()
```

``` output
Rows: 131
Columns: 14
$ F_liv                  <list> [<data.frame[1 x 2]>], [<data.frame[3 x 2]>], …
$ `_remitters`           <list> [<data.frame[0 x 0]>], [<data.frame[0 x 0]>], …
$ E18_months_no_water    <list> <NULL>, <"Aug", "Sept">, <NULL>, <NULL>, <NULL…
$ F05_money_source       <list> <NULL>, <NULL>, <NULL>, <NULL>, <NULL>, <NULL>…
$ E_yes_group            <list> [<data.frame[0 x 0]>], [<data.frame[3 x 14]>],…
$ D_plots                <list> [<data.frame[2 x 8]>], [<data.frame[3 x 8]>], …
$ F_items                <list> [<data.frame[3 x 3]>], [<data.frame[2 x 3]>], …
$ F10_liv_owned          <list> "poultry", <"oxen", "cows", "goats">, "none", …
$ F14_items_owned        <list> <"bicycle", "television", "solar_panel", "tabl…
$ G02_months_lack_food   <list> "Jan", <"Jan", "Sept", "Oct", "Nov", "Dec">, <…
$ E22_res_change         <list> <NULL>, <NULL>, <NULL>, <NULL>, <NULL>, <NULL>…
$ `_members`             <list> [<data.frame[3 x 12]>], [<data.frame[7 x 12]>]…
$ G03_no_food_mitigation <list> <"na", "rely_less_food", "reduce_meals", "day_…
$ E_no_group             <list> [<data.frame[2 x 6]>], [<data.frame[0 x 0]>], …
```

So what can we do about *F\_liv*, the column of dataframes? Well first things first, we can access each one. For  example to access the dataframe in the first row, we can use the  bracket (`[`) subsetting. Here we use single bracket, but you could also use double bracket (`[[`). The `[[` form allows only a single element to be selected using integer or character indices, whereas `[` allows indexing by vectors.


``` r
json_data$F_liv[1]
```

``` output
[[1]]
  F11_no_owned F_curr_liv
1            1    poultry
```

We can also choose to view the nested dataframes at all the rows of our main dataframe where a particular condition is met (for example where the value for the variable *C06\_rooms* is equal to 4):


``` r
json_data$F_liv[which(json_data$C06_rooms == 4)]
```

``` output
[[1]]
  F11_no_owned F_curr_liv
1            3       oxen
2            2       cows
3            5      goats

[[2]]
  F11_no_owned F_curr_liv
1            4       oxen
2            5       cows
3            3      goats

[[3]]
data frame with 0 columns and 0 rows

[[4]]
  F11_no_owned F_curr_liv
1            4       oxen
2            4       cows
3            4      goats
4            1      sheep

[[5]]
  F11_no_owned F_curr_liv
1            2       cows
```

## Write the JSON file to csv

If we try to write our json\_data dataframe to a csv as we would usually in a regular dataframe, we won't get the desired result. Using the `write_csv` function from the `{readr}` package won't give you an error for list columns, but you'll only see missing (i.e. `NA`) values in these columns. Let's try it out to confirm:


``` r
write_csv(json_data, "json_data_with_list_columns.csv")
read_csv("json_data_with_list_columns.csv")
```

To write out as a csv while maintaining the data within the list columns, we will need to "flatten" these columns. One way to do this is to convert these list columns into character types. (However, we don't want to change the data types for any of the other columns). Here's one way to do this using tidyverse. This command only applies the `as.character` command to those columns 'where' `is.list` is `TRUE`.


``` r
flattened_json_data <- json_data %>% 
  mutate(across(where(is.list), as.character))
flattened_json_data
```

``` output
# A tibble: 131 × 74
   C06_rooms B19_grand_liv A08_ward E01_water_use B18_sp_parents_liv
       <int> <chr>         <chr>    <chr>         <chr>             
 1         1 no            ward2    no            yes               
 2         1 yes           ward2    yes           yes               
 3         1 no            ward2    no            no                
 4         1 no            ward2    no            no                
 5         1 yes           ward2    no            no                
 6         1 no            ward2    no            no                
 7         1 yes           ward2    yes           no                
 8         3 yes           ward1    yes           yes               
 9         1 yes           ward2    yes           no                
10         5 no            ward2    yes           no                
# ℹ 121 more rows
# ℹ 69 more variables: B16_years_liv <int>, E_yes_group_count <chr>,
#   F_liv <chr>, `_note2` <lgl>, instanceID <chr>, B20_sp_grand_liv <chr>,
#   F10_liv_owned_other <lgl>, `_note1` <lgl>, F12_poultry <chr>,
#   D_plots_count <chr>, C02_respondent_wall_type_other <lgl>,
#   C02_respondent_wall_type <chr>, C05_buildings_in_compound <int>,
#   `_remitters` <chr>, E18_months_no_water <chr>, F07_use_income <chr>, …
```

Now you can write this to a csv file:


``` r
write_csv(flattened_json_data, "data_output/json_data_with_flattened_list_columns.csv")
```

Note: this means that when you read this csv back into R, the column of the nested dataframes will now be read in as a character vector. Converting it back to list to extract elements might be complicated, so it is probably better to keep storing these data in a JSON format if you will have to do this.

You can also write out the individual nested dataframes to a csv. For example:


``` r
write_csv(json_data$F_liv[[1]], "data_output/F_liv_row1.csv")
```





## Load required packages


``` r
#Load required packages

require(quanteda)
```

``` output
Loading required package: quanteda
```

``` output
Package version: 4.1.0
Unicode version: 14.0
ICU version: 70.1
```

``` output
Parallel computing: disabled
```

``` output
See https://quanteda.io for tutorials and examples.
```

``` r
require(ggplot2)
require(tidyverse)
require(here)
```

``` output
Loading required package: here
```

``` output
here() starts at /home/runner/work/media-test/media-test/site/built
```

``` r
require(quanteda.textstats)
```

``` output
Loading required package: quanteda.textstats
```

``` r
#install.packages("quanteda.textplots",dependencies =T,repos = "http://cran.us.r-project.org")
#install.packages("udpipe",dependencies =T,repos = "http://cran.us.r-project.org")
#install.packages("spacyr",dependencies =T,repos = "http://cran.us.r-project.org")
require(quanteda.textplots)
```

``` output
Loading required package: quanteda.textplots
```

``` r
require(udpipe)
```

``` output
Loading required package: udpipe
```

``` r
require(spacyr)
```

``` output
Loading required package: spacyr
```

## CL Corpus linguistics 

A collection of different methods which are related by the fact that they are performed on large collections of electronically stored, naturally occurring texts.

-   Many quantitative and/or make use of statistical tests,

-   Performed by computer software

-    CL requires considerable human input, which often includes qualitative analysis (such as examining concordance lines).

-   Related to NLP or natural language processing (subfield of computer science and artificial intelligence).

(Baker et al 2008:273)

## CDA Critical discourse analysis 

A way of doing discourse analysis from a critical perspective, which often focuses on theoretical concepts such as power, ideology and domination. (Baker et al, 2008)

-   Tends towards qualitative approach

-   Social, political, historical and intertextual contexts

## Frequency

Goal - Introducing the uses and potential dangers of frequency lists and wordclouds

Frequency is a key concept underpinning the analysis of text and corpora.

Nonetheless, as a purely quantitative measure it needs to be used with a sensitivity to

1.  The word-distribution patterns in human languages.

2.  The importance of context for meaning

## Frequency lists

-   Help direct researcher's investigations
-   Measures of dispersion can reveal trends across texts
-   Can provide a sociological profile of a given word or phrase

## Many potential problems

-   Can be reductive and generalising
-   Can oversimplify
-   Focus on differences between word distributions can obscure more interesting interpretations.

## Why frequency?

-   Language is not a random affair - rule based / rule-generating
-   Words tend to occur in relationship to other words with high levels of predictability!
-   Rule-generating - people have choices "No terms are neutral. Choice of words expresses an ideological position". (Stubbs, 1996:107; Baker, 47)
-   "If people speak or write in an unexpected way, or make one linguistic choice over another, more obvious one, then that reveals something about their intentions, whether conscious or not." (Baker:48)
-   Only certain choices are available at any one time (e.g. historically)


``` r
# load file with video metadata (you tube data tools)
videos <- read_csv(
  here("data","youtube-27082024-open-refine-200-na.csv"),
  na= ""
)
```

``` output
Rows: 200 Columns: 32
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr  (22): channel_id, channel_title, video_id, url, published_at_sql, video...
dbl   (8): position, randomise, year, month, day, duration_sec, view_count, ...
lgl   (1): caption
dttm  (1): published_at

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
videos_text <- videos %>%
  rename(text = video_description)  %>%
  rename(post_id = video_id) %>%
  rename(date = published_at_sql) %>%
  rename(category = video_category_label) %>%
  subset(select = c(post_id,text,date,category) )

d <- corpus(videos_text) %>%
  tokens(remove_punct=T) %>%
  dfm() 
 # dfm_remove(mystopwords)


tstat_freq_d <- textstat_frequency(d, n = 60)

feature_freq <- ggplot(tstat_freq_d, aes(x = frequency, y = reorder(feature, frequency))) +
  geom_point() +
  labs(x = "Frequency", y = "Feature")
feature_freq
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-14-1.png" style="display: block; margin: auto;" />

## Grammatical/Function words

These words are most commonly used in a language and seldom change.

For this reason, they are often included on lists of "stop words"

As our "corpus" is generated as a match to a specific query, we should include our query words in our stop lists.

Can compare to standard corpus e.g. frequency of "you" in advertising

## Lexical words

The frequencies of lexical words show us what a text or corpus is about.

## Defining stopwords

Here are some stopwords that will be useful for this corpus:


``` r
## exclude common english words
mystopwords <- stopwords("english",
                         source="snowball")
## exclude search terms, branding features and other spam
mystopwords <- c("news","via","newzroom","dstv","social","media","visit","us",
                 "online","sabcnews.com","copyright","clicks","hair",
                 "advertisement","ad","advertise","south","africa",
                 "ft","thumbnail","kabza","semi","tee","vigro","njelic","mthuda"
                 ,"ntokzin","focalistic",
                 "na",
                 mystopwords)
```

## Removing stopwords

Now we can chart frequencies for the most common lexical words.


``` r
d <- d %>%
  dfm_remove(mystopwords)

tstat_freq_d <- textstat_frequency(d, n = 60)

feature_freq <- ggplot(tstat_freq_d, aes(x = frequency, y = reorder(feature, frequency))) +
  geom_point() +
  labs(x = "Frequency", y = "Feature")
feature_freq
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-16-1.png" style="display: block; margin: auto;" />

## Wordclouds and visualisation

Wordclouds are a popular visualisation format for frequencies because they allow us to focus on meanings.

Can you think of some of the disadvantages of representing data in this way?


``` r
d = corpus(videos_text) %>%
  tokens(remove_punct=T) %>%
  dfm() %>%
  dfm_remove(mystopwords)
textplot_wordcloud(d, max_words=100)
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-17-1.png" style="display: block; margin: auto;" />

## Visualising frequencies in other corpora

Frequency charts
Here we will use a frequency chart to compare the words used in the video description text of our sample with the words used by Google Vision to describe key frames from the videos.


``` r
#load file with Google vision machine learning labels for video keyframes
labels <- read_csv(
  here("data","machine_learning","convert-google-vision-to-csv-f7fdf13f677ad1f913e22e79c0446dc3.csv"),
  na= ""
)
```

``` output
Rows: 6251 Columns: 3
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (3): labelAnnotations, fullTextAnnotation, image_file

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
labels_text <- labels %>%
  rename(text = labelAnnotations)  %>%
  mutate(post_id=paste(image_file)) %>%
  subset(select = c(post_id,text) )

mystopwords <- stopwords("english",
                         source="snowball")
#mystopwords <- c("clicks","hair","advertisement","ad","south","africa","na",mystopwords)
#glue("Now {length(mystopwords)} stopwords:")

d = corpus(labels_text) %>%
  tokens(remove_punct=T) %>%
  dfm() %>%
  dfm_remove(mystopwords)


tstat_freq_d <- textstat_frequency(d, n = 60)

feature_freq <- ggplot(tstat_freq_d, aes(x = frequency, y = reorder(feature, frequency))) +
  geom_point() +
  labs(x = "Frequency", y = "Feature")
feature_freq
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-18-1.png" style="display: block; margin: auto;" />


``` output
Loading required package: wordcloud2
```

``` output
Loading required package: RColorBrewer
```

<!--html_preserve--><div class="wordcloud2 html-widget html-fill-item" id="htmlwidget-9eca9c4c91b339b0155a" style="width:504px;height:504px;"></div>
<script type="application/json" data-for="htmlwidget-9eca9c4c91b339b0155a">{"x":{"word":["customer","floor","fixture","luggage","bags","door","job","flooring","standing","building","event","hand","gesture","dish","thumb","food","cuisine","drink","ingredient","wood","finger","skin","ear","happy","leisure","eyelash","eyebrow","beauty","fun","hairstyle","eyewear","smile","community","neck","stole","retail","fashion","design","chin","hair","eye","facial","expression","forehead","organ","face","twig","electric","blue","hardwood","grey","darkness","font","pattern","midnight","jaw","hearing","vision","care","glasses","mouth","nose","necklace","audio","equipment","chest","human","body","glass","accessory","sunglasses","jewelry","sleeve","jewellery","curtain","shoulder","photograph","cool","team","trousers","red","recreation","t-shirt","hat","baseball","cap","shorts","shirt","jeans","clothing","crowd","headgear","carmine","fan","audience","public","space","rebellion","product","mammal","city","magenta","art","chair","suit","engineering","stadium","vest","arena","snapshot","escalator","stairs","automotive","windshield","motor","vehicle","personal","luxury","car","lighting","exterior","tints","shades","hood","mirror","tire","steering","wheel","window","air","travel","workwear","auto","part","beard","family","passenger","sitting","plant","parking","road","liquid","ceiling","room","metal","transport","rim","cricket","shopping","mode","pedestrian","bumper","youth","jersey","visual","arts","competition","player","sport","venue","handrail","steel","jacket","flag","sports","crew","world","field","house","stage","sportswear","goggles","protective","championship","logo","advertising","machine","shield","idiophone","fictional","character","leg","fence","helmet","uniform","microphone","coat","tradition","street","sun","town","belt","television","backpack","games","elbow","gadget","entertainment","costume","outerwear","bag","thigh","organism","physical","fitness","performing","polo","interaction","pink","social","group","textile","paint","artist","fur","moustache","flesh","knit","beanie","beret","ushanka","natural","material","coquelicot","macro","photography","petal","peach","santa","claus","holiday","festival","cloud","sky","display","device","tourism","hoodie","conversation","gas","muscle","athlete","glove","child","service","ritual","rite","employment","temple","active","tie","fedora","people","wind","instrument","arm","watch","windbreaker","scarf","head","zombie","cooking","fiction","mask","gear","footwear","blazer","cleanliness","winter","official","sweatpant","light","facade","signage","electronic","graphics","line","night","bird","parrot","feather","vertebrate","snout","wool","tail","wing","rectangle","comfort","collar","apron","security","military","person","high-visibility","hard","purple","cosplay","balloon","tartan","mario","blind","shooting","tree","top","close-up","rose","order","linens","baby","toddler","dye","camouflage","marines","army","soldier","toy","non-commissioned","officer","organization","monk","ceremony","adaptation","lifejacket","bottle","sneakers","shoe","musical","boxing","trunk","waist","justice","league","barechested","wrist","digital","camera","cameras","optics","reflex","photographer","sleeveless","shadow","christmas","snow","precipitation","party","supply","fruit","system","geological","phenomenon","cumulus","landscape","atmospheric","woolen","cornrows","mobile","phone","bow","formal","wear","selfie","nail","lamp","superman","boot","covering","squad","brand","grille","technology","walking","property","wrinkle","ball","dance","football","friendship","lens","white","dastar","knee","denim","joint","black","calf","knee-high","freezing","working","animal","pack","performance","musician","music","hulk","green","lap","dress","protest","masque","chinese","new","year","sharing","headphones","science","sign","language","gun","buzz","cut","throat","video","videographer","cinematographer","operator","megaphone","documentary","water","single-lens","yellow","umbrella","smoke","journalist","woodwind","shotgun","celebrating","environment","morning","exhibition","emergency","firefighter","reflection","orange","brick","painting","handshake","polar","fleece","american","government","agency","law","enforcement","white-collar","worker","terrestrial","photomontage","daylighting","cheek","lip","scar","skyscraper","parka","wall","foot","grass","drawing","stalagmite","stalactite","recipe","asphalt","abdomen","stuffed","kiss","shawl","rain","earrings","clown","slr","mirrorless","interchangeable-lens","back","publication","avengers","symmetry","chanter","train","drum","afro","bovine","livestock","statue","sculpture","tread","beak","wig","sphere","cotton","candy","wine","communication","action","film","turban","flowering","dog","breed","companion","dairy","cow","concert","flash","drinking","shelf","trade","convenience","store","shelving","pet","plastic","hospital","gown","medical","health","cargo","pants","greeting","mascot","peaked","brass","spokesperson","presenter","news","interior","headpiece","pedicel","laptop","speech","software","screenshot","history","circle","number","multimedia","blond","program","chef","computer","photo","caption","projector","presentation","address","speaking","tower","block","orator","liver","led-backlit","lcd","flat","panel","poster","set","portable","communications","azure","astronomical","object","globe","map","atmosphere","tournament","bat-and-ball","game","cricketer","nature","soil","surface","lipstick","liner","document","triangle","parallel","slope","clock","iris","yoga","pant","weights","strength","training","jheri","curl","monochrome","portrait","graphic","brassiere","ringlet","nickel","handle","major","appliance","aluminium","web","page","transparent","colorfulness","collage","urban","table","band","plays","picture","frame","layered","architecture","tile","mass","production","industry","factory","black-and-white","style","newscaster","real","estate","pleased","media","step","cutting","astronomy","horizon","cabinetry","box","composite","concrete","grocery","smartphone","daytime","billboard","output","star","metropolitan","area","sidewalk","telephone","booth","siding","desk","coloring","violet","stain","aqua","telephony","cylinder","fluid","tooth","stomach","solution","cosmetics","symbol","dishware","tableware","wedding","barware","stemware","drinkware","cosmetic","dentistry","makeover","artificial","integrations","creative","brown","cookware","bakeware","cable","wire","aluminum","can","tin","beverage","flower","arranging","photographic","grooming","bun","croydon","facelift","solvent","chemical","compound","stopper","saver","alcoholic","payment","card","label","long","dreadlocks","felidae","handwriting","book","bookcase","monitor","keyboard","desktop","home","jigsaw","puzzle","one-piece","garment","cartoon","deep","frying","baked","goods","fast","fried","produce","french","fries","meat","pakora","gluten","drawer","staple","panko","fritter","dessert","button","serveware","suburb","indoor","shipping","cowboy","square","boa","cocktail","underwater","diving","divemaster","cup","coffee","piercing","trademark","learning"],"freq":[91,12,40,132,132,40,224,75,23,111,6121,98,1589,16,183,44,18,17,18,109,106,62,80,569,1446,336,195,11,3543,86,458,381,237,124,1,69,184,173,117,428,23,392,112,329,42,83,15,151,158,17,21,36,578,91,21,196,8,83,86,45,217,142,31,126,745,81,236,31,59,63,74,6,1741,114,3,27,31,93,1806,297,877,1702,3799,3414,2160,5402,605,302,405,108,3118,753,324,640,69,632,16,16,239,29,73,1255,116,15,147,56,9,163,5,59,7,8,178,15,87,114,593,8,14,40,43,81,81,15,7,36,2,8,44,7,72,44,18,30,831,3,33,19,35,1,14,15,18,271,36,46,3,122,13,4,13,9,156,110,53,110,305,53,12,13,6,6,578,43,87,54,57,1,5,40,184,53,573,16,139,154,32,8,8,300,300,97,2,40,327,198,40,559,26,84,1,86,153,40,9,59,28,388,247,286,57,116,8,3,3,56,45,56,148,198,198,41,25,22,498,160,150,10,13,27,1,12,24,7,5,82,16,7,14,14,59,13,9,47,130,201,9,68,29,39,144,4,99,41,26,1,3,3,70,53,44,37,127,3,10,49,64,69,20,73,16,12,2,8,17,15,50,8,147,34,2,7,35,56,42,23,20,3,2,1,8,32,2,7,3,3,77,7,81,7,35,361,127,6,2,5,4,7,6,2,4,6,46,11,1,5,2,2,1,4,2,170,40,17,20,5,19,21,6,1,3,38,2,26,11,32,4,1,9,26,3,3,6,35,9,166,14,14,12,30,3,8,2,5,1,12,9,1,4,1,2,1,8,1,1,2,17,16,3,49,49,9,30,1,2,14,3,22,116,8,70,5,13,4,6,5,4,3,8,12,1,40,8,27,81,9,1,11,13,20,5,4,2,27,3,18,1,32,3,2,3,3,3,62,3,7,14,8,4,6,7,9,39,19,4,3,3,1,6,5,12,2,1,4,2,1,32,2,1,5,2,1,2,5,3,9,1,2,2,2,11,11,3,3,11,11,8,2,1,94,133,1,2,2,6,9,6,3,1,1,15,11,5,2,1,1,1,24,2,1,1,1,1,14,2,7,1,4,2,2,1,3,1,3,1,1,8,2,1,1,1,31,1,2,1,1,3,2,1,1,1,2,74,2,21,15,5,6,13,1,13,2,2,1,2,6,7,1,1,1,1,43,68,129,9,5,1,14,11,49,128,3,33,15,109,1,56,5,35,36,36,1,9,2,4,1,1,1,6,1,1,4,4,4,8,3,3,2,5,5,1,1,2,1,1,1,1,1,2,5,42,1,5,2,6,5,2,3,1,1,1,1,1,24,24,5,1,5,1,2,1,1,1,3,2,3,3,2,1,3,5,7,1,1,12,12,3,1,2,2,2,1,1,2,2,7,1,1,1,7,1,1,1,4,4,3,2,1,1,1,1,1,3,1,1,1,1,1,1,2,3,2,3,5,1,1,6,10,3,1,3,4,3,1,17,1,2,1,6,3,3,7,2,2,1,3,1,1,1,1,1,3,1,2,2,1,1,2,2,1,1,1,2,2,1,1,1,1,1,1,3,1,1,4,3,3,4,3,1,2,1,1,4,4,1,6,6,3,3,10,10,6,7,7,1,2,1,1,3,1,1,1,1,1,1,1,1,1,1,1,1,2,3,1,3,1,1,1,1],"fontFamily":"Segoe UI","fontWeight":"bold","color":["#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#C6DBEF","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#3182BD","#9ECAE1","#6BAED6","#3182BD","#C6DBEF","#08519C","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C"],"minSize":0,"weightFactor":0.02940695964711649,"backgroundColor":"white","gridSize":0,"minRotation":0,"maxRotation":0,"shuffle":true,"rotateRatio":0.4,"shape":"circle","ellipticity":0.65,"figBase64":null,"hover":null},"evals":[],"jsHooks":{"render":[{"code":"function(el,x){\n                        console.log(123);\n                        if(!iii){\n                          window.location.reload();\n                          iii = False;\n\n                        }\n  }","data":null}]}}</script><!--/html_preserve-->



## Beyond frequency

Need to go beyond simple frequency - context plays an important role in meaning.

For discourse analysis, frequent clusters of words are more revealing than just looking at individual words in isolation

Therefore need to consider: - keyness, - collocation and - concordances.

## Keyness

"Keyness is defined as the statistically significantly higher frequency of particular words or clusters in the corpus under analysis in comparison with another corpus, either a general reference corpus, or a comparable specialised corpus. Its purpose is to point towards the "aboutness" of a text or homogeneous corpus (Scott, 1999), that is, its topic, and the central elements of its content."

## Comparing keyness across categories


``` r
d = corpus(videos_text)


# Create subcorpus from the News/poltics and People/Blogs + Entertainment categories
corp_category <- corpus_subset(d,
                 category %in% c("News & Politics",
                                 "Entertainment","People & Blogs"))

# Create a dfm grouped by category
dfmat_cat <- tokens(corp_category, remove_punct = TRUE,
                    remove_symbols=TRUE, remove_url = T) |>
  tokens_keep("^\\p{LETTER}", valuetype="regex")|>
  tokens_remove(mystopwords) |>
  tokens_group(groups = category) |>
  dfm()

# Calculate keyness and determine News as target
tstat_keyness <- textstat_keyness(dfmat_cat, target = "News & Politics")

# Plot estimated word keyness
textplot_keyness(tstat_keyness)
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-21-1.png" style="display: block; margin: auto;" />

## Collocation

The above-chance frequent co-occurrence of two words within a pre-determined span, usually five words on either side of the word under investigation (the node) (see Sinclair, 1991)

The statistical calculation of collocation is based on three measures:

-   The frequency the node,

-   The frequency of the collocates, and

-   The frequency of the collocation.

"Because the collocates of a node contribute to its meaning (Nattinger and De Carrico, 1992), they can provide 'a semantic analysis of a word' (Sinclair, 1991), but can also 'convey messages implicitly' (Hunston, 2002)." (Baker)

We will explore collocation in the most commented videos in our random sample (n=35)


``` r
#for youtube comments
comments <- read_csv(
  here("data","sample_60_all_comments.csv"),
  na= ""
)
```

``` output
Rows: 3417 Columns: 13
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr  (10): id, replyCount, authorName, text, authorChannelId, authorChannelU...
dbl   (2): likeCount, isReply
dttm  (1): publishedAt

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


``` r
comments_text <- comments %>%
  rename(date = publishedAt)  %>%
  mutate(post_id=paste(id,authorName,sep = "_")) %>%
  subset(select = c(post_id,date,text) )

d <- corpus(comments_text)%>%
  tokens(remove_punct=T)%>%
  tokens_remove(mystopwords)

toks_comments <- tokens(d, remove_punct = TRUE)
tstat_col_caps <- tokens_select(toks_comments, pattern = "^[A-Z]",
                                valuetype = "regex",
                                case_insensitive = FALSE,
                                padding = TRUE) %>%
  textstat_collocations(min_count = 5)
head(tstat_col_caps, 20)
```

``` output
     collocation count count_nested length    lambda         z
1   south africa    58            0      2  7.423782 28.299700
2  south african    21            0      2  5.689695 19.581277
3 south africans    15            0      2  6.104332 16.714600
4  julius malema     9            0      2  6.801750 15.070387
5      FINE FLAT     5            0      2  9.624543 11.588454
6  pure egyptian     6            0      2  9.540098 10.086395
7 money strategy     5            0      2 11.059720  9.898023
8       viva EFF     7            0      2  5.051634  9.716125
9    aneeza gold    10            0      2 11.469704  7.731952
```

``` r
dfm <- d %>%
  dfm()

topfeatures(dfm)
```

``` output
 black   hair people    eff  white clicks    can   like   just racist 
   747    703    702    456    407    388    357    346    331    271 
```


## Concordances

Also referred to as a key word in context (kwic)



``` r
kw_comp <- kwic(toks_comments, pattern = c("person*", "people*"))
head(kw_comp, 10)
```

``` output
Keyword-in-context with 10 matches.                                                                   
  [text21, 6] @Thokomagonya Ndlovu brother atleast job |  people  |
  [text25, 1]                                          |  person  |
  [text25, 7]        realises ad also commenting white | person's |
  [text37, 5]                         EFF mercy us old |  people  |
 [text45, 11]         blind EFF embodies keeping black |  people  |
  [text57, 4]                      Malema just tweeted |  people  |
  [text58, 3]                  @lsgtsoo3298yes tweeted |  people  |
  [text60, 4]                   @lsgtsoo3298 say black |  people  |
  [text60, 8]   black people particular problem assume |  people  |
 [text60, 12]             assume people say think said |  people  |
                                             
 anything Take care brother                  
 realises ad also commenting white           
 hair Adverts tell us hair                   
 need medicstion                             
 work just know better ways                  
 gathered lol trust leads                    
 gathered ignorant think independently master
 particular problem assume people say        
 say think said people used                  
 used pronoun means speaking general         
```

``` r
#We will select two tokens objects for words inside and outside of the 10-word windows of the keywords (sa).

eff <- c("eff","fighter*")
white_people <- c("white people", "whites")
black_people <- c("black people", "blacks")
keyword <- black_people
#keyword <- white_people
toks_inside <- tokens_keep(toks_comments, pattern = keyword, window = 6)
toks_inside <- tokens_remove(toks_inside, pattern = keyword) # remove the keywords
toks_outside <- tokens_remove(toks_comments, pattern = keyword, window = 6)

#We can compute words’ association with the keywords using textstat_keyness().
dfmat_inside <- dfm(toks_inside)
dfmat_outside <- dfm(toks_outside)

tstat_key_inside <- textstat_keyness(rbind(dfmat_inside, dfmat_outside),
                                     target = seq_len(ndoc(dfmat_inside)))
tstat_key_inside <- arrange(tstat_key_inside,p)
head(tstat_key_inside, 50)
```

``` output
         feature      chi2            p n_target n_reference
1         whites 204.58938 0.000000e+00       12          40
2              2 164.53968 0.000000e+00       10          34
3  investigation  97.41770 0.000000e+00        2           0
4       included  63.95410 1.221245e-15        2           1
5       coloured  41.55397 1.146612e-10        5          31
6           team  25.49025 4.446235e-07        4          30
7    #justsaying  21.31712 3.892395e-06        1           0
8             13  21.31712 3.892395e-06        1           0
9       @melin24  21.31712 3.892395e-06        1           0
10      abelungu  21.31712 3.892395e-06        1           0
11    afrikaaner  21.31712 3.892395e-06        1           0
12        agrees  21.31712 3.892395e-06        1           0
13           ala  21.31712 3.892395e-06        1           0
14    apologises  21.31712 3.892395e-06        1           0
15         babes  21.31712 3.892395e-06        1           0
16      belonged  21.31712 3.892395e-06        1           0
17         birth  21.31712 3.892395e-06        1           0
18         cared  21.31712 3.892395e-06        1           0
19        clciks  21.31712 3.892395e-06        1           0
20      coconuts  21.31712 3.892395e-06        1           0
21  collectively  21.31712 3.892395e-06        1           0
22  demonization  21.31712 3.892395e-06        1           0
23      devacurl  21.31712 3.892395e-06        1           0
24            ef  21.31712 3.892395e-06        1           0
25     exhibited  21.31712 3.892395e-06        1           0
26         fking  21.31712 3.892395e-06        1           0
27      flighted  21.31712 3.892395e-06        1           0
28          grew  21.31712 3.892395e-06        1           0
29      immgrant  21.31712 3.892395e-06        1           0
30  impoverished  21.31712 3.892395e-06        1           0
31      incensed  21.31712 3.892395e-06        1           0
32   inequitably  21.31712 3.892395e-06        1           0
33 institutional  21.31712 3.892395e-06        1           0
34       keepers  21.31712 3.892395e-06        1           0
35           l8a  21.31712 3.892395e-06        1           0
36      labeling  21.31712 3.892395e-06        1           0
37        lookup  21.31712 3.892395e-06        1           0
38       loosing  21.31712 3.892395e-06        1           0
39    non-whites  21.31712 3.892395e-06        1           0
40        outlet  21.31712 3.892395e-06        1           0
41        owners  21.31712 3.892395e-06        1           0
42  problem.with  21.31712 3.892395e-06        1           0
43        prompt  21.31712 3.892395e-06        1           0
44         pssst  21.31712 3.892395e-06        1           0
45         scott  21.31712 3.892395e-06        1           0
46          shun  21.31712 3.892395e-06        1           0
47          slow  21.31712 3.892395e-06        1           0
48   socialistic  21.31712 3.892395e-06        1           0
49         taxes  21.31712 3.892395e-06        1           0
50     townships  21.31712 3.892395e-06        1           0
```

## Other important linguistic features

These are important linguistic features for discourse analysis. Do you think that we can investigate them using frequency measures?

-   pronoun use
-   modality
-   metaphors
-   agency
-   passivisation
-   nominalisation etc

:::::::::::::::::::::::::::::::::::::::: keypoints

- JSON is a popular data format for transferring data used by a great many Web based APIs
- The complex structure of a JSON document means that it cannot easily be 'flattened' into tabular data
- We can use R code to extract values of interest and place them in a csv file

::::::::::::::::::::::::::::::::::::::::::::::::::


