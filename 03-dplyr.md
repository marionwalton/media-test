---
title: Data Wrangling with dplyr
teaching: 25
exercises: 15
source: Rmd
---


:::: instructor

- This lesson works better if you have graphics demonstrating dplyr commands.
  You can modify [this Google Slides deck](https://docs.google.com/presentation/d/1A9abypFdFp8urAe9z7GCMjFr4aPeIb8mZAtJA2F7H0w/edit#slide=id.g652714585f_0_114) and use it for your workshop.
- For this lesson make sure that learners are comfortable using pipes.
- There is also sometimes some confusion on what the arguments of `group_by`
  should be, and when to use `filter()` and `select()`.

::::::::::::


::::::::::::::::::::::::::::::::::::::: objectives

- Describe the purpose of an R package and the **`dplyr`** package.
- Select certain columns in a dataframe with the **`dplyr`** function `select`.
- Select certain rows in a dataframe according to filtering conditions with the **`dplyr`** function `filter`.
- Link the output of one **`dplyr`** function to the input of another function with the 'pipe' operator `%>%`.
- Add new columns to a dataframe that are functions of existing columns with `mutate`.
- Use the split-apply-combine concept for data analysis.
- Use `summarize`, `group_by`, and `count` to split a dataframe into groups of observations, apply a summary statistics for each group, and then combine the results.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I select specific rows and/or columns from a dataframe?
- How can I combine multiple commands into a single command?
- How can I create new columns or remove existing columns from a dataframe?

::::::::::::::::::::::::::::::::::::::::::::::::::

**`dplyr`** is a package for making tabular data wrangling easier by using a
limited set of functions that can be combined to extract and summarize insights
from your data.

Like **`readr`**, **`dplyr`** is a part of the tidyverse. These packages were loaded
in R's memory when we called `library(tidyverse)` earlier.

:::::::::::::::::::::::::::::::::::::::::  callout

## Note

The packages in the tidyverse, namely **`dplyr`**, **`tidyr`** and **`ggplot2`**
accept both the British (e.g. *summarise*) and American (e.g. *summarize*) spelling
variants of different function and option names. For this lesson, we utilize
the American spellings of different functions; however, feel free to use
the regional variant for where you are teaching.

::::::::::::::::::::::::::::::::::::::::::::::::::

## What is an R package?

The package **`dplyr`** provides easy tools for the most common data
wrangling tasks. It is built to work directly with dataframes, with many
common tasks optimized by being written in a compiled language (C++) (not all R
packages are written in R!).

There are also packages available for a wide range of tasks including building plots
(**`ggplot2`**, which we'll see later), downloading data from the NCBI database, or
performing statistical analysis on your data set. Many packages such as these are
housed on, and downloadable from, the **C**omprehensive **R** **A**rchive **N**etwork
(CRAN) using `install.packages`. This function makes the package accessible by your R
installation with the command `library()`, as you did with `tidyverse` earlier.

To easily access the documentation for a package within R or RStudio, use
`help(package = "package_name")`.

To learn more about **`dplyr`** after the workshop, you may want to check out this
[handy data transformation with **`dplyr`** cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-transformation.pdf).

:::::::::::::::::::::::::::::::::::::::::  callout

## Note

There are alternatives to the `tidyverse` packages for data wrangling, including
the package [`data.table`](https://rdatatable.gitlab.io/data.table/). See this
[comparison](https://mgimond.github.io/rug_2019_12/Index.html)
for example to get a sense of the differences between using `base`, `tidyverse`, and
`data.table`.

::::::::::::::::::::::::::::::::::::::::::::::::::

## Learning **`dplyr`**

To make sure everyone will use the same dataset for this lesson, we'll read
again the SAFI dataset that we downloaded earlier.



``` r
library(tidyverse)
library(here)

videos <- read_csv(
  here("data", "youtube-27082024-open-refine-200-na.csv"), 
  na = "na")
```

``` r
## inspect the data
videos
```

``` output
# A tibble: 200 × 32
   position randomise channel_id               channel_title      video_id url  
      <dbl>     <dbl> <chr>                    <chr>              <chr>    <chr>
 1      112       409 UCI3RT5PGmdi1KVp9FG_CneA eNCA               iPUAl1j… http…
 2       50       702 UCI3RT5PGmdi1KVp9FG_CneA eNCA               YUmIAd_… http…
 3      149       313 UCMwDXpWEVQVw4ZF7z-E4NoA StellenboschNews … v8XfpOi… http…
 4      167       384 UCsqKkYLOaJ9oBwq9rxFyZMw SOUTH AFRICAN POL… lnLdo2k… http…
 5      195       606 UC5G5Dy8-mmp27jo6Frht7iQ Umgosi Entertainm… XN6toca… http…
 6      213       423 UCC1udUghY9dloGMuvZzZEzA The Tea World      rh2Nz78… http…
 7      145       452 UCaCcVtl9O3h5en4m-_edhZg Celeb LaLa Land    1l5GZ0N… http…
 8      315       276 UCAurTjb6Ewz21vjfTs1wZxw NOSIPHO NZAMA      j4Y022C… http…
 9      190       321 UCBlX1mnsIFZRqsyRNvpW_rA Zandile Mhlambi    gf2YNN6… http…
10      214       762 UClY87IoUANFZtswyC9GeecQ Beauty recipes     AGJmRd4… http…
# ℹ 190 more rows
# ℹ 26 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, …
```

``` r
## preview the data
# view(videos)
```

We're going to learn some of the most common **`dplyr`** functions:

- `select()`: subset columns
- `filter()`: subset rows on conditions
- `mutate()`: create new columns by using information from other columns
- `group_by()` and `summarize()`: create summary statistics on grouped data
- `arrange()`: sort results
- `count()`: count discrete values

## Selecting columns and filtering rows

To select columns of a dataframe, use `select()`. The first argument to this
function is the dataframe (`videos`), and the subsequent arguments are the
columns to keep, separated by commas. Alternatively, if you are selecting
columns adjacent to each other, you can use a `:` to select a range of columns,
read as "select columns from \_\_\_ to \_\_\_." You may have done something similar in
the past using subsetting. `select()` is essentially doing the same thing as
subsetting, using a package (`dplyr`) instead of R's base functions.


``` r
# to select columns throughout the dataframe
select(videos, channel_title, view_count, )
# to do the same thing with subsetting
videos[c("channel_title","view_count","comment_count")]
# to select a series of connected columns
select(videos,"view_count":"comment_count")
# to select columns by name as well as a series of connected columns
select(videos,"channel_title","published_at_sql","view_count":"comment_count")
```

To choose rows based on specific criteria, we can use the `filter()` function.
The argument after the dataframe is the condition we want our final
dataframe to adhere to (e.g. channel_title name is SABC News):


``` r
# filters observations where channel title is "SABC News"
filter(videos, channel_title == "SABC News")
```

``` output
# A tibble: 22 × 32
   position randomise channel_id               channel_title video_id    url    
      <dbl>     <dbl> <chr>                    <chr>         <chr>       <chr>  
 1       30       785 UC8yH-uI81UUtEMDsowQyx1g SABC News     PNom9RIla7o https:…
 2       63       418 UC8yH-uI81UUtEMDsowQyx1g SABC News     EZDazXhf-Pk https:…
 3       65       486 UC8yH-uI81UUtEMDsowQyx1g SABC News     9ewtc8eUY7k https:…
 4        9       380 UC8yH-uI81UUtEMDsowQyx1g SABC News     naLVyfMrFGs https:…
 5       14       500 UC8yH-uI81UUtEMDsowQyx1g SABC News     pO8dkGtjxQc https:…
 6       17       647 UC8yH-uI81UUtEMDsowQyx1g SABC News     rRg8J3lqaPc https:…
 7       36       663 UC8yH-uI81UUtEMDsowQyx1g SABC News     TLAxmusluVw https:…
 8       66       541 UC8yH-uI81UUtEMDsowQyx1g SABC News     P82g09FOSQ0 https:…
 9       72       810 UC8yH-uI81UUtEMDsowQyx1g SABC News     N2d1eBI5Zjc https:…
10      132       404 UC8yH-uI81UUtEMDsowQyx1g SABC News     JXz_3WWo3ew https:…
# ℹ 12 more rows
# ℹ 26 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, …
```

We can also specify multiple conditions within the `filter()` function. We can
combine conditions using either "and" or "or" statements. In an "and"
statement, an observation (row) must meet **every** criteria to be included
in the resulting dataframe. To form "and" statements within dplyr, we can  pass
our desired conditions as arguments in the `filter()` function, separated by
commas:


``` r
# filters observations with "and" operator (comma)
# output dataframe satisfies ALL specified conditions
filter(videos, channel_title == "SABC News",
                   view_count > 1000,
                   comment_count > 20)
```

``` output
# A tibble: 16 × 32
   position randomise channel_id               channel_title video_id    url    
      <dbl>     <dbl> <chr>                    <chr>         <chr>       <chr>  
 1       30       785 UC8yH-uI81UUtEMDsowQyx1g SABC News     PNom9RIla7o https:…
 2       65       486 UC8yH-uI81UUtEMDsowQyx1g SABC News     9ewtc8eUY7k https:…
 3        9       380 UC8yH-uI81UUtEMDsowQyx1g SABC News     naLVyfMrFGs https:…
 4       36       663 UC8yH-uI81UUtEMDsowQyx1g SABC News     TLAxmusluVw https:…
 5       66       541 UC8yH-uI81UUtEMDsowQyx1g SABC News     P82g09FOSQ0 https:…
 6       72       810 UC8yH-uI81UUtEMDsowQyx1g SABC News     N2d1eBI5Zjc https:…
 7       11       209 UC8yH-uI81UUtEMDsowQyx1g SABC News     752qt6_0J0k https:…
 8        5       636 UC8yH-uI81UUtEMDsowQyx1g SABC News     AqQ-ukdgnWc https:…
 9       23       628 UC8yH-uI81UUtEMDsowQyx1g SABC News     CAwHz26tboE https:…
10       34       569 UC8yH-uI81UUtEMDsowQyx1g SABC News     I5Qra7UUnJs https:…
11       62       461 UC8yH-uI81UUtEMDsowQyx1g SABC News     mTfSl9KnNPo https:…
12       73       659 UC8yH-uI81UUtEMDsowQyx1g SABC News     _aH8qJLdKtI https:…
13       18       597 UC8yH-uI81UUtEMDsowQyx1g SABC News     B2SsMOgP8sQ https:…
14       22       769 UC8yH-uI81UUtEMDsowQyx1g SABC News     dGDBWhcPTuI https:…
15       67        33 UC8yH-uI81UUtEMDsowQyx1g SABC News     fs_xxqx70mA https:…
16       85       574 UC8yH-uI81UUtEMDsowQyx1g SABC News     FIgI7GIcsuc https:…
# ℹ 26 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, location_description <chr>,
#   view_count <dbl>, like_count <dbl>, favorite_count <dbl>, …
```

We can also form "and" statements with the `&` operator instead of commas:


``` r
# filters observations with "&" logical operator
# output dataframe satisfies ALL specified conditions
filter(videos, channel_title == "SABC News" &
                   view_count > 1000 &
                   comment_count > 20)
```

``` output
# A tibble: 16 × 32
   position randomise channel_id               channel_title video_id    url    
      <dbl>     <dbl> <chr>                    <chr>         <chr>       <chr>  
 1       30       785 UC8yH-uI81UUtEMDsowQyx1g SABC News     PNom9RIla7o https:…
 2       65       486 UC8yH-uI81UUtEMDsowQyx1g SABC News     9ewtc8eUY7k https:…
 3        9       380 UC8yH-uI81UUtEMDsowQyx1g SABC News     naLVyfMrFGs https:…
 4       36       663 UC8yH-uI81UUtEMDsowQyx1g SABC News     TLAxmusluVw https:…
 5       66       541 UC8yH-uI81UUtEMDsowQyx1g SABC News     P82g09FOSQ0 https:…
 6       72       810 UC8yH-uI81UUtEMDsowQyx1g SABC News     N2d1eBI5Zjc https:…
 7       11       209 UC8yH-uI81UUtEMDsowQyx1g SABC News     752qt6_0J0k https:…
 8        5       636 UC8yH-uI81UUtEMDsowQyx1g SABC News     AqQ-ukdgnWc https:…
 9       23       628 UC8yH-uI81UUtEMDsowQyx1g SABC News     CAwHz26tboE https:…
10       34       569 UC8yH-uI81UUtEMDsowQyx1g SABC News     I5Qra7UUnJs https:…
11       62       461 UC8yH-uI81UUtEMDsowQyx1g SABC News     mTfSl9KnNPo https:…
12       73       659 UC8yH-uI81UUtEMDsowQyx1g SABC News     _aH8qJLdKtI https:…
13       18       597 UC8yH-uI81UUtEMDsowQyx1g SABC News     B2SsMOgP8sQ https:…
14       22       769 UC8yH-uI81UUtEMDsowQyx1g SABC News     dGDBWhcPTuI https:…
15       67        33 UC8yH-uI81UUtEMDsowQyx1g SABC News     fs_xxqx70mA https:…
16       85       574 UC8yH-uI81UUtEMDsowQyx1g SABC News     FIgI7GIcsuc https:…
# ℹ 26 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, location_description <chr>,
#   view_count <dbl>, like_count <dbl>, favorite_count <dbl>, …
```

In an "or" statement, observations must meet *at least one* of the specified conditions.
To form "or" statements we use the logical operator for "or," which is the vertical bar (|):


``` r
# filters observations with "|" logical operator
# output dataframe satisfies AT LEAST ONE of the specified conditions
filter(videos, channel_title == "SABC News" | channel_title == "eNCA")
```

``` output
# A tibble: 42 × 32
   position randomise channel_id               channel_title video_id    url    
      <dbl>     <dbl> <chr>                    <chr>         <chr>       <chr>  
 1      112       409 UCI3RT5PGmdi1KVp9FG_CneA eNCA          iPUAl1jywdU https:…
 2       50       702 UCI3RT5PGmdi1KVp9FG_CneA eNCA          YUmIAd_O0U4 https:…
 3       30       785 UC8yH-uI81UUtEMDsowQyx1g SABC News     PNom9RIla7o https:…
 4       63       418 UC8yH-uI81UUtEMDsowQyx1g SABC News     EZDazXhf-Pk https:…
 5       65       486 UC8yH-uI81UUtEMDsowQyx1g SABC News     9ewtc8eUY7k https:…
 6        2       944 UCI3RT5PGmdi1KVp9FG_CneA eNCA          li3_91gCQHc https:…
 7        3       269 UCI3RT5PGmdi1KVp9FG_CneA eNCA          kvQRfnD1h64 https:…
 8        4       518 UCI3RT5PGmdi1KVp9FG_CneA eNCA          3BkmO0M56lA https:…
 9        7       417 UCI3RT5PGmdi1KVp9FG_CneA eNCA          hZBwMrCCp4A https:…
10        9       380 UC8yH-uI81UUtEMDsowQyx1g SABC News     naLVyfMrFGs https:…
# ℹ 32 more rows
# ℹ 26 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, …
```

## Pipes

What if you want to select and filter at the same time? There are three
ways to do this: use intermediate steps, nested functions, or pipes.

With intermediate steps, you create a temporary dataframe and use
that as input to the next function, like this:


``` r
videos2 <- filter(videos, channel_title == "SABC News")
videos_SABC_metrics <- select(videos2, channel_title,view_count:comment_count)
```

This is readable, but can clutter up your workspace with lots of objects that
you have to name individually. With multiple steps, that can be hard to keep
track of.

You can also nest functions (i.e. one function inside of another), like this:


``` r
videos_SABC_metrics <- select(filter(videos, channel_title == "SABC News"),
                         channel_title,view_count:comment_count)
videos_SABC_metrics
```

``` output
# A tibble: 22 × 5
   channel_title view_count like_count favorite_count comment_count
   <chr>              <dbl>      <dbl>          <dbl>         <dbl>
 1 SABC News          22079        125              0           105
 2 SABC News           3339         18              0             7
 3 SABC News          20674        122              0           100
 4 SABC News          19715         68              0           132
 5 SABC News          14485         52              0            19
 6 SABC News           2329         18              0             7
 7 SABC News          25313        133              0           131
 8 SABC News          11466         55              0            28
 9 SABC News           7297         74              0            34
10 SABC News           3147         12              0             2
# ℹ 12 more rows
```

This is handy, but can be difficult to read if too many functions are nested, as
R evaluates the expression from the inside out (in this case, filtering, then
selecting).

The last option, *pipes*, are a recent addition to R. Pipes let you take the
output of one function and send it directly to the next, which is useful when
you need to do many things to the same dataset. There are two Pipes in R: 1) `%>%` (called magrittr pipe; made available via the **`magrittr`** package, installed automatically with
**`dplyr`**) or 2) `|>` (called native R pipe and it comes preinstalled with R v4.1.0 onwards). Both the pipes are, by and large, function similarly with a few differences (For more information, check: https://www.tidyverse.org/blog/2023/04/base-vs-magrittr-pipe/). The choice of which pipe to be used can be changed in the Global settings in R studio and once that is done, you can type the pipe with:

- <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>M</kbd> if you have a PC or <kbd>Cmd</kbd> +
  <kbd>Shift</kbd> + <kbd>M</kbd> if you have a Mac.


``` r
# the following example is run using magrittr pipe but the output will be same with the native pipe
videos %>%
    filter(channel_title == "SABC News") %>%
    select(channel_title,view_count:comment_count)
```

``` output
# A tibble: 22 × 5
   channel_title view_count like_count favorite_count comment_count
   <chr>              <dbl>      <dbl>          <dbl>         <dbl>
 1 SABC News          22079        125              0           105
 2 SABC News           3339         18              0             7
 3 SABC News          20674        122              0           100
 4 SABC News          19715         68              0           132
 5 SABC News          14485         52              0            19
 6 SABC News           2329         18              0             7
 7 SABC News          25313        133              0           131
 8 SABC News          11466         55              0            28
 9 SABC News           7297         74              0            34
10 SABC News           3147         12              0             2
# ℹ 12 more rows
```

``` r
#videos |>
#   filter(channel_title == "SABC News") |>
#   select(channel_title,view_count:comment_count)
```

In the above code, we use the pipe to send the `videos` dataset first
through `filter()` to keep rows where `channel_title` is "SABC News", then through
`select()` to keep only the columns from `channel_title` to `respondent_wall_type`. Since `%>%`
takes the object on its left and passes it as the first argument to the function
on its right, we don't need to explicitly include the dataframe as an argument
to the `filter()` and `select()` functions any more.

Some may find it helpful to read the pipe like the word "then". For instance,
in the above example, we take the dataframe `videos`, *then* we `filter`
for rows with `channel_title == "SABC News"`, *then* we `select` columns `view_count:comment_count`.
The **`dplyr`** functions by themselves are somewhat simple,
but by combining them into linear workflows with the pipe, we can accomplish
more complex data wrangling operations.

If we want to create a new object with this smaller version of the data, we
can assign it a new name:


``` r
videos_SABC_metrics <- videos %>%
    filter(channel_title == "SABC News") %>%
    select(view_count:comment_count)

videos_SABC_metrics
```

``` output
# A tibble: 22 × 4
   view_count like_count favorite_count comment_count
        <dbl>      <dbl>          <dbl>         <dbl>
 1      22079        125              0           105
 2       3339         18              0             7
 3      20674        122              0           100
 4      19715         68              0           132
 5      14485         52              0            19
 6       2329         18              0             7
 7      25313        133              0           131
 8      11466         55              0            28
 9       7297         74              0            34
10       3147         12              0             2
# ℹ 12 more rows
```

Note that the final dataframe (`videos_SABC_metrics`) is the leftmost part of this
expression.

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Using pipes, subset the `videos` data to include videos
where the video is categorised as "News & Politics" 
and retain only the metrics columns with values (views, likes and comments).

:::::::::::::::  solution

## Solution


``` r
videos %>%
    filter(video_category_label == "News & Politics") %>%
    select(view_count,like_count,comment_count)
```

``` output
# A tibble: 85 × 3
   view_count like_count comment_count
        <dbl>      <dbl>         <dbl>
 1        939         12            NA
 2        910          7            NA
 3        213          6             2
 4      22079        125           105
 5       3339         18             7
 6      20674        122           100
 7       6745         10            NA
 8       6087         47             0
 9      11359         84             0
10      34911        299            NA
# ℹ 75 more rows
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Mutate

Frequently you'll want to create new columns based on the values in existing
columns, for example to do unit conversions, or to find the ratio of values in
two columns. For this we'll use `mutate()`.

We might be interested in the ratio of likes
to comments:


``` r
videos %>%
    mutate(ratio = like_count / comment_count)
```

``` output
# A tibble: 200 × 33
   position randomise channel_id               channel_title      video_id url  
      <dbl>     <dbl> <chr>                    <chr>              <chr>    <chr>
 1      112       409 UCI3RT5PGmdi1KVp9FG_CneA eNCA               iPUAl1j… http…
 2       50       702 UCI3RT5PGmdi1KVp9FG_CneA eNCA               YUmIAd_… http…
 3      149       313 UCMwDXpWEVQVw4ZF7z-E4NoA StellenboschNews … v8XfpOi… http…
 4      167       384 UCsqKkYLOaJ9oBwq9rxFyZMw SOUTH AFRICAN POL… lnLdo2k… http…
 5      195       606 UC5G5Dy8-mmp27jo6Frht7iQ Umgosi Entertainm… XN6toca… http…
 6      213       423 UCC1udUghY9dloGMuvZzZEzA The Tea World      rh2Nz78… http…
 7      145       452 UCaCcVtl9O3h5en4m-_edhZg Celeb LaLa Land    1l5GZ0N… http…
 8      315       276 UCAurTjb6Ewz21vjfTs1wZxw NOSIPHO NZAMA      j4Y022C… http…
 9      190       321 UCBlX1mnsIFZRqsyRNvpW_rA Zandile Mhlambi    gf2YNN6… http…
10      214       762 UClY87IoUANFZtswyC9GeecQ Beauty recipes     AGJmRd4… http…
# ℹ 190 more rows
# ℹ 27 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, …
```

We may be interested in investigating whether mentioning the EFF in the video title
 had any effect on the ratio of likes to comments. 
 
 To look at this relationship, we will first remove
data from our dataset where comments were not recorded.
These cases are recorded as "NA" in the dataset.

To remove these cases, we could insert a `filter()` in the chain:


``` r
videos %>%
    filter(!is.na(comment_count)) %>%
    mutate(ratio = like_count / comment_count)
```

``` output
# A tibble: 184 × 33
   position randomise channel_id               channel_title      video_id url  
      <dbl>     <dbl> <chr>                    <chr>              <chr>    <chr>
 1      149       313 UCMwDXpWEVQVw4ZF7z-E4NoA StellenboschNews … v8XfpOi… http…
 2      167       384 UCsqKkYLOaJ9oBwq9rxFyZMw SOUTH AFRICAN POL… lnLdo2k… http…
 3      195       606 UC5G5Dy8-mmp27jo6Frht7iQ Umgosi Entertainm… XN6toca… http…
 4      213       423 UCC1udUghY9dloGMuvZzZEzA The Tea World      rh2Nz78… http…
 5      145       452 UCaCcVtl9O3h5en4m-_edhZg Celeb LaLa Land    1l5GZ0N… http…
 6      315       276 UCAurTjb6Ewz21vjfTs1wZxw NOSIPHO NZAMA      j4Y022C… http…
 7      190       321 UCBlX1mnsIFZRqsyRNvpW_rA Zandile Mhlambi    gf2YNN6… http…
 8      214       762 UClY87IoUANFZtswyC9GeecQ Beauty recipes     AGJmRd4… http…
 9      263       952 UCYeHXDmIJDiF1DVQM0qfNWQ Mama Shirat        19uG9pR… http…
10       30       785 UC8yH-uI81UUtEMDsowQyx1g SABC News          PNom9RI… http…
# ℹ 174 more rows
# ℹ 27 more variables: published_at <dttm>, published_at_sql <chr>, year <dbl>,
#   month <dbl>, day <dbl>, video_title <chr>, video_description <chr>,
#   tags <chr>, video_category_label <chr>, topic_categories <chr>,
#   duration_sec <dbl>, definition <chr>, caption <lgl>,
#   default_language <chr>, default_l_audio_language <chr>,
#   thumbnail_maxres <chr>, licensed_content <dbl>, …
```

The `!` symbol negates the result of the `is.na()` function. Thus, if `is.na()`
returns a value of `TRUE` (because the `comment_count` is missing), the `!` symbol
negates this and says we only want values of `FALSE`, where `comment_count **is
not** missing.

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Create a new dataframe from the `videos` data that meets the following
criteria: contains only the `channel_title` column and, for all the videos which received at least one comment,
 a new column called
`ratio` containing a value that is equal to the likes divided by the comments.
Only the rows where `ratio` is greater than 20 should be shown in the
final dataframe.

**Hint**: think about how the commands should be ordered to produce this data
frame!

:::::::::::::::  solution

## Solution


``` r
videos_ratio <- videos %>%
    filter(comment_count>0) %>%
    mutate(ratio =  view_count/comment_count) %>%
    filter(ratio > 20) %>%
    select(channel_title, ratio)
videos_ratio
```

``` output
# A tibble: 120 × 2
   channel_title   ratio
   <chr>           <dbl>
 1 The Tea World    143.
 2 Celeb LaLa Land  260.
 3 Zandile Mhlambi  106.
 4 Beauty recipes  3707.
 5 Mama Shirat      650.
 6 SABC News        210.
 7 SABC News        477 
 8 SABC News        207.
 9 SABC News        149.
10 SABC News        762.
# ℹ 110 more rows
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Split-apply-combine data analysis and the summarize() function

Many data analysis tasks can be approached using the *split-apply-combine*
paradigm: split the data into groups, apply some analysis to each group, and
then combine the results. **`dplyr`** makes this very easy through the use of
the `group_by()` function.

### The `summarize()` function

`group_by()` is often used together with `summarize()`, which collapses each
group into a single-row summary of that group.  `group_by()` takes as arguments
the column names that contain the **categorical** variables for which you want
to calculate the summary statistics. So to compute the average household size by
channel_title:


``` r
videos %>%
    group_by(channel_title) %>%
    summarize(mean_views = mean(view_count))
```

``` output
# A tibble: 119 × 2
   channel_title                 mean_views
   <chr>                              <dbl>
 1 2nacheki                         54470. 
 2 Absolute Controversy               102  
 3 African Diaspora News Channel    18255  
 4 Al Jazeera English              113704  
 5 Ayanda Mafuyeka                     29.5
 6 Azana Jezile                     65647  
 7 BANELE NOCUZE                      210  
 8 Banetsi Tshetlo                      7  
 9 Beauty recipes                  263208  
10 Blackish Blue TV                   281  
# ℹ 109 more rows
```

You may also have noticed that the output from these calls doesn't run off the
screen anymore. It's one of the advantages of `tbl_df` over dataframe.

You can also group by multiple columns:


``` r
videos %>%
    group_by(channel_title, video_category_label) %>%
    summarize(mean_views = mean(view_count))
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 120 × 3
# Groups:   channel_title [119]
   channel_title                 video_category_label mean_views
   <chr>                         <chr>                     <dbl>
 1 2nacheki                      News & Politics         54470. 
 2 Absolute Controversy          People & Blogs            102  
 3 African Diaspora News Channel News & Politics         18255  
 4 Al Jazeera English            News & Politics        113704  
 5 Ayanda Mafuyeka               People & Blogs             29.5
 6 Azana Jezile                  Howto & Style           65647  
 7 BANELE NOCUZE                 People & Blogs            210  
 8 Banetsi Tshetlo               Entertainment               7  
 9 Beauty recipes                Howto & Style          263208  
10 Blackish Blue TV              News & Politics           281  
# ℹ 110 more rows
```

Note that the output is a grouped tibble. To obtain an ungrouped tibble, use the
`ungroup` function:


``` r
videos %>%
    group_by(channel_title, video_category_label) %>%
    summarize(mean_views = mean(view_count)) %>%
    ungroup()
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 120 × 3
   channel_title                 video_category_label mean_views
   <chr>                         <chr>                     <dbl>
 1 2nacheki                      News & Politics         54470. 
 2 Absolute Controversy          People & Blogs            102  
 3 African Diaspora News Channel News & Politics         18255  
 4 Al Jazeera English            News & Politics        113704  
 5 Ayanda Mafuyeka               People & Blogs             29.5
 6 Azana Jezile                  Howto & Style           65647  
 7 BANELE NOCUZE                 People & Blogs            210  
 8 Banetsi Tshetlo               Entertainment               7  
 9 Beauty recipes                Howto & Style          263208  
10 Blackish Blue TV              News & Politics           281  
# ℹ 110 more rows
```

When grouping both by `channel_title` and `default_l_audio_language`, we see rows in our table for
videos where the creator did not specify their default audio language. 
We can exclude those data from our table using a filter step.


``` r
videos %>%
    filter(!is.na(default_l_audio_language)) %>%
    group_by(channel_title, default_l_audio_language) %>%
    summarize(mean_views = mean(view_count))
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 59 × 3
# Groups:   channel_title [59]
   channel_title                               default_l_audio_lang…¹ mean_views
   <chr>                                       <chr>                       <dbl>
 1 2nacheki                                    en-US                      54470.
 2 African Diaspora News Channel               en                         18255 
 3 Azana Jezile                                en-US                      65647 
 4 Beauty recipes                              en                        263208 
 5 Buhle N                                     en-GB                        405 
 6 By.MonaLisa                                 zxx                         3802 
 7 DoRo Lungani                                zu                         19648 
 8 Duke University - The Fuqua School of Busi… en                          4932 
 9 E News Mzansi                               en                          2876.
10 Economic Freedom Fighters                   en-US                       7164 
# ℹ 49 more rows
# ℹ abbreviated name: ¹​default_l_audio_language
```

Once the data are grouped, you can also summarize multiple variables at the same
time (and not necessarily on the same variable). For instance, we could add a
column indicating the minimum household size for each channel_title for each group
(members of an irrigation association vs not):


``` r
videos %>%
    filter(!is.na(default_l_audio_language)) %>%
    group_by(channel_title, default_l_audio_language) %>%
    summarize(mean_views = mean(view_count),
              max_views = max(view_count))
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 59 × 4
# Groups:   channel_title [59]
   channel_title                     default_l_audio_lang…¹ mean_views max_views
   <chr>                             <chr>                       <dbl>     <dbl>
 1 2nacheki                          en-US                      54470.     98894
 2 African Diaspora News Channel     en                         18255      18255
 3 Azana Jezile                      en-US                      65647      65647
 4 Beauty recipes                    en                        263208     263208
 5 Buhle N                           en-GB                        405        405
 6 By.MonaLisa                       zxx                         3802       3802
 7 DoRo Lungani                      zu                         19648      39223
 8 Duke University - The Fuqua Scho… en                          4932       4932
 9 E News Mzansi                     en                          2876.      5066
10 Economic Freedom Fighters         en-US                       7164      10996
# ℹ 49 more rows
# ℹ abbreviated name: ¹​default_l_audio_language
```

It is sometimes useful to rearrange the result of a query to inspect the values.
For instance, we can sort on `max_views` to put the group with the smallest
household first:


``` r
videos %>%
    filter(!is.na(default_l_audio_language)) %>%
    group_by(channel_title, default_l_audio_language) %>%
    summarize(mean_views = mean(view_count),
              max_views = max(view_count)) %>%
    arrange(max_views)
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 59 × 4
# Groups:   channel_title [59]
   channel_title                     default_l_audio_lang…¹ mean_views max_views
   <chr>                             <chr>                       <dbl>     <dbl>
 1 Lit News                          en-GB                          19        19
 2 Peppers Echo                      en                             23        23
 3 GOLDGATOR TV                      en-GB                          30        30
 4 TalkOutLoud                       en-GB                          67        67
 5 Ile Eko Omoluabi                  en                            103       103
 6 Papa Khwatsi TOPIC                en-GB                         105       105
 7 The Upright Man                   en                            113       113
 8 Fresh Trendz                      en                            130       130
 9 Hidden Truth State of Decay Sout… en-GB                         151       151
10 Newcastle Advertiser              en-GB                         172       172
# ℹ 49 more rows
# ℹ abbreviated name: ¹​default_l_audio_language
```

To sort in descending order, we need to add the `desc()` function. If we want to
sort the results by decreasing order of views, or reverse alphabetical order of the audio language
 to see languages other than english:



``` r
videos %>%
    filter(!is.na(default_l_audio_language)) %>%
    group_by(channel_title, default_l_audio_language) %>%
    summarize(mean_views = mean(view_count),
              max_views = max(view_count)) %>%
    arrange(desc(max_views))
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 59 × 4
# Groups:   channel_title [59]
   channel_title   default_l_audio_language mean_views max_views
   <chr>           <chr>                         <dbl>     <dbl>
 1 SABC News       en                           38085.    487961
 2 Beauty recipes  en                          263208     263208
 3 eNCA            en                           46485.    138612
 4 Eyewitness News en-GB                       103949     103949
 5 2nacheki        en-US                        54470.     98894
 6 Morexskinglow   en                           91347      91347
 7 News24          en-GB                        72606.     73622
 8 Azana Jezile    en-US                        65647      65647
 9 Renaldo Gouws   en                           50694      56890
10 Mama Shirat     en-US                        41626      41626
# ℹ 49 more rows
```

``` r
videos %>%
    filter(!is.na(default_l_audio_language)) %>%
    group_by(channel_title, default_l_audio_language) %>%
    summarize(mean_views = mean(view_count),
              max_views = max(view_count)) %>%
    arrange(desc(default_l_audio_language))
```

``` output
`summarise()` has grouped output by 'channel_title'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 59 × 4
# Groups:   channel_title [59]
   channel_title             default_l_audio_language mean_views max_views
   <chr>                     <chr>                         <dbl>     <dbl>
 1 By.MonaLisa               zxx                           3802       3802
 2 DoRo Lungani              zu                           19648      39223
 3 NOSIPHO NZAMA             zu                             597        597
 4 2nacheki                  en-US                        54470.     98894
 5 Azana Jezile              en-US                        65647      65647
 6 Economic Freedom Fighters en-US                         7164      10996
 7 Gemini Baby               en-US                          435        435
 8 Grey Net                  en-US                         2619       2619
 9 KHATHA WORLDWIDE          en-US                          490        490
10 Mama Shirat               en-US                        41626      41626
# ℹ 49 more rows
```

### Counting

When working with data, we often want to know the number of observations found
for each factor or combination of factors. For this task, **`dplyr`** provides
`count()`. For example, if we wanted to count the number of rows of data for
each channel_title, we would do:


``` r
videos %>%
    count(channel_title)
```

``` output
# A tibble: 119 × 2
   channel_title                     n
   <chr>                         <int>
 1 2nacheki                          2
 2 Absolute Controversy              1
 3 African Diaspora News Channel     1
 4 Al Jazeera English                1
 5 Ayanda Mafuyeka                   2
 6 Azana Jezile                      1
 7 BANELE NOCUZE                     3
 8 Banetsi Tshetlo                   1
 9 Beauty recipes                    1
10 Blackish Blue TV                  1
# ℹ 109 more rows
```

For convenience, `count()` provides the `sort` argument to get results in
decreasing order:


``` r
videos %>%
    count(channel_title, sort = TRUE)
```

``` output
# A tibble: 119 × 2
   channel_title                 n
   <chr>                     <int>
 1 SABC News                    22
 2 eNCA                         20
 3 Newzroom Afrika              13
 4 Umgosi Entertainment          5
 5 StellenboschNews Com          4
 6 BANELE NOCUZE                 3
 7 Economic Freedom Fighters     3
 8 Renaldo Gouws                 3
 9 Trade with Free Money         3
10 2nacheki                      2
# ℹ 109 more rows
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Which YouTube video categories are used to describe the videos in the sample? (Use the video_category_label column.) 
Create a tibble of these categories and the number of videos in each of them, arranged in descending order.

:::::::::::::::  solution

## Solution


``` r
videos %>%
   count(video_category_label) %>%
   arrange(desc(n))
```

``` output
# A tibble: 10 × 2
   video_category_label      n
   <chr>                 <int>
 1 News & Politics          85
 2 People & Blogs           62
 3 Entertainment            23
 4 Howto & Style            14
 5 Comedy                    6
 6 Education                 4
 7 Film & Animation          3
 8 Autos & Vehicles          1
 9 Nonprofits & Activism     1
10 Science & Technology      1
```

:::::::::::::::::::::::::

Use `group_by()` and `summarize()` to find the mean, min, and max
number of views for each YouTube channel (channel_title). Also add the number of
observations (hint: see `?n`).  Arrange the channels in descending order by the channel's maximum views.

:::::::::::::::  solution

## Solution


``` r
videos %>%
  group_by(channel_title) %>%
  summarize(
      mean_views = mean(view_count),
      min_views = min(view_count),
      max_views = max(view_count),
      n = n()  )%>%
      arrange(desc(max_views))
```

``` output
# A tibble: 119 × 5
   channel_title      mean_views min_views max_views     n
   <chr>                   <dbl>     <dbl>     <dbl> <int>
 1 SABC News              36450.      2329    487961    22
 2 Beauty recipes        263208     263208    263208     1
 3 SamupertyZuluTV       144836     144836    144836     1
 4 eNCA                   36810.       910    138612    20
 5 Al Jazeera English    113704     113704    113704     1
 6 Eyewitness News       103949     103949    103949     1
 7 2nacheki               54470.     10047     98894     2
 8 Morexskinglow          91347      91347     91347     1
 9 Newzroom Afrika         9501.       246     83542    13
10 Kay Monqo              81906      81906     81906     1
# ℹ 109 more rows
```

:::::::::::::::::::::::::

Excluding those videos that were set to not allow comments (comment_count =NA),
 what was the total number of comments posted on the videos in each month?

:::::::::::::::  solution

## Solution


``` r
# if not already included, add month, year, and day columns
library(lubridate) # load lubridate if not already loaded
videos %>%
    filter(!is.na(comment_count)) %>%
    mutate(month = month(published_at_sql),
           day = day(published_at_sql),
           year = year(published_at_sql)) %>%
    group_by(year, month) %>%
    summarize(total_comments = sum(comment_count)) %>%
    arrange(year,month)
```

``` output
`summarise()` has grouped output by 'year'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 20 × 3
# Groups:   year [4]
    year month total_comments
   <dbl> <dbl>          <dbl>
 1  2020     1             33
 2  2020     2              0
 3  2020     4            168
 4  2020     7            466
 5  2020     8             29
 6  2020     9          11215
 7  2020    10              1
 8  2020    11              1
 9  2021     6              0
10  2021     9             17
11  2021    12             64
12  2022     1             14
13  2022     2            386
14  2022     3              4
15  2022     4              8
16  2022     7             76
17  2022    11             71
18  2023     1              7
19  2023     3              6
20  2023     6            241
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Use the `dplyr` package to manipulate dataframes.
- Use `select()` to choose variables from a dataframe.
- Use `filter()` to choose data based on values.
- Use `group_by()` and `summarize()` to work with subsets of data.
- Use `mutate()` to create new variables.

::::::::::::::::::::::::::::::::::::::::::::::::::


