---
site: sandpaper::sandpaper_site
---

Data Carpentry's aim is to teach researchers basic concepts, skills,
and tools for working with data so that they can get more done in
less time, and with less pain. The lessons below were designed for
those interested in working with social sciences data in R.

This is an introduction to R designed for participants with no
programming experience. These lessons can be taught in a half-day,
full-day, or over a two-day workshop (see
[Instructor Notes](https://datacarpentry.org/r-socialsci/instructor/instructor-notes.html)
for suggested lesson plans).
They start with some basic information about R syntax, the
RStudio interface, and move through how to import CSV files, the
structure of data frames, how to deal with factors, how to add/remove
rows and columns, how to calculate summary statistics from a data
frame, and a brief introduction to plotting.

::::::::::::::::::::::::::::::::::::::::::  prereq

## Getting Started

Data Carpentry's teaching is hands-on, so participants are encouraged to use
their own computers to ensure the proper setup of tools for an efficient
workflow.

**These lessons assume no prior knowledge of the skills or tools.**

To get started, follow the directions in the "[Setup](setup.html)" tab to
download data to your computer and follow any installation instructions.

#### Prerequisites

This lesson requires a working copy of **R** and **RStudio**.
<br>To most effectively use these materials, please make sure to install
everything *before* working through this lesson.

## About the example data

We will be working with a dataset of 200 Youtube posts. Here is some background to the dataset.

- An initial dataset of 314 posts were returned by the YouTube API in response to the following query: "Query: clicks south africa* hair (ad OR advertisement) -click" covering videos posted during the period 2020 - 2023
- The dataset was prepared using a spreadsheet and OpenRefine to identify missing values, change variable names to "snake case", change tags and topics to lowercase, and to use semicolons to separate tags and topics.
- Owing to the ambiguity of the keyword "clicks", many irrelevant results were returned. - The dataset was reviewed and any posts which were not related to the controversy, or which did not relate to issues about body politics and racism were excluded.
- This resulted in a dataset of 200 posts selected for computational analysis.
In case you need a refresher, here are some details about how [YouTube API](https://supermetrics.com/docs/integration-youtube-public-data-fields/) derives the variables in this dataset.


::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::  instructor

## For Instructors

If you are teaching this lesson in a workshop, please see the
[Instructor notes](https://datacarpentry.org/r-socialsci/instructor/instructor-notes.html)
for helpful tips.


::::::::::::::::::::::::::::::::::::::::::::::::::


