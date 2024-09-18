---
title: Text as data (Optional)
teaching: 30
exercises: 15
source: Rmd
---



:::: instructor

- This is an optional lesson intended to introduce approaches to text as data by applying
  key concepts from corpus linguistics and natural language processing, with a focus on using
   computational approaches to qualitatively oriented discourse analysis.
- Note that his lesson was community-contributed and remains a work in progress. As such, it could
  benefit from feedback from instructors and/or workshop participants.

::::::::::::

::::::::::::::::::::::::::::::::::::::: objectives

- Introduce key concepts needed to use the **`quanteda`** package for discourse analysis 
i.e. corpus, tokens, dfm, frequency, collocation, keyness, concordance.
- Prepare text for analysis with the **`quanteda`** functions `corpus`, `tokens`, `dfm`, and `dfm_remove`.
- Investigate the most frequent features in a dfm using the **`quanteda`** function `textstat_frequency`.
- Plot frequencies using **`ggplot`** 
- Select and modify a list of stopwords to remove unwanted words, query terms and spam from a dataset. 
- Visualise frequencies as a wordcloud using the **`quanteda`** function `textplot_wordcloud` and the **`wordcloud2`** function  `wordcloud2`.
- Identify the strengths and weaknesses of these approaches to visualising text data.



::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- What are the most important concepts for automating text analysis?
- How can I prepare text data for analysis?
- How can I visualise frequency, collocation and concordance for a corpus of textual data?

::::::::::::::::::::::::::::::::::::::::::::::::::

## Load quanteda package


``` r
require(quanteda)
```


## Computational linguistics and Corpus Linguistics

Computational linguistics and Corpus Linguistics are two related areas of study in linguistics. Both of these areas provide approaches media scholars can use when analysing textual data in media texts.

Computational linguistics is a broad inter-disciplinary area of study where software and algorithms are developed to analyse and synthesise language and speech for applications such as machine translation, speech recognition, machine learning and deep learning ("AI"). Corpus linguistics has developed methods to study trends and patterns in language use by analysing large collections of electronically stored, naturally occurring texts. Both of these areas are related to Natural Language Processing (NLP) which is a subfield of computer science.

While Computational Linguistics has a strongly quantitative focus, Corpus Linguistics often includes qualitative analysis (such as examining concordance lines).  Corpus linguistics involves much qualitative work interpreting text, and so can be used to extend the scope of traditional media studies approaches to linguistic discourse such as Critical Discourse Analysis (CDA).

For more see:

(Baker et al 2008:273)

## Corpus

A *corpus* is a set of documents which stores large quantities of real-life text. The plural form of the word is *corpora*.

You can find a set of South African language corpora on the [SADILAR corpus portal](https://corpus.sadilar.org/corpusportal/explore/corpus) website.

## Document-Term Matrix

Computers work by using numbers, and so corpora are analysed by generating a numerical representations of text.

A popular representation of text in CL is the *Document-Term Matrix* or *DTM* (also known as Term-Document Matrix (TDM) or Document-Feature Matrix (DTM). 
A DTM represents a corpus as a large table (known as a matrix). Each document has a separate row, each word has a separate column, and each cell has a number showing how often a particular word appears in a particular document.

We will study a small example of a document-term matrix, using this famous quotation from Nelson Mandela's 1964 speech during the Rivonia Trial: 

"I have cherished the ideal of a democratic and free society in which all persons will live together in harmony and with equal opportunities. It is an idea for which I hope to live for and to see realized. But, my Lord, if it needs be, it is an ideal for which I am prepared to die."

For the purposes of this example, we will treat each sentence as a separate "text".



``` r
texts <- c(
      "I have cherished the ideal of a democratic and free society in which all persons will live together in harmony and with equal opportunities", 
"It is an idea for which I hope to live for and to see realized", 
"But my Lord if it needs be it is an ideal for which I am prepared to die")

d <- tokens(texts) %>%
  dfm()

convert(d, "matrix") 
```

``` output
       features
docs    i have cherished the ideal of a democratic and free society in which
  text1 1    1         1   1     1  1 1          1   2    1       1  2     1
  text2 1    0         0   0     0  0 0          0   1    0       0  0     1
  text3 1    0         0   0     1  0 0          0   0    0       0  0     1
       features
docs    all persons will live together harmony with equal opportunities it is
  text1   1       1    1    1        1       1    1     1             1  0  0
  text2   0       0    0    1        0       0    0     0             0  1  1
  text3   0       0    0    0        0       0    0     0             0  2  1
       features
docs    an idea for hope to see realized but my lord if needs be am prepared
  text1  0    0   0    0  0   0        0   0  0    0  0     0  0  0        0
  text2  1    1   2    1  2   1        1   0  0    0  0     0  0  0        0
  text3  1    0   1    0  1   0        0   1  1    1  1     1  1  1        1
       features
docs    die
  text1   0
  text2   0
  text3   1
```

This representation loses the poetry of repetition and the carefully crafted dramatic conclusion of the four hour long speech. Instead the numeric matrix format allows Mandela's linguistic choices in 1964 to be more easily compared to his other famous speeches, or to speeches made by other political leaders and statesmen.

In this DTM each sentence has a separate row. Each word has its own column. The number in the cells show how often a particular word appears in a particular sentence. 



:::::::::::::::::::::::::::::::::::::::: keypoints

- Use the **`quanteda`** package to analyse text data.
- Use `corpus()`, `tokens()`,`dfm()`, `dfm_remove()` and stopword lists to prepare text for analysis.
- Use `textstat_frequency` to investigate the most frequently used tokens or features in a dfm. 
- Plot frequencies using **`ggplot`** and the **`quanteda`** function `textplot_wordcloud`.

::::::::::::::::::::::::::::::::::::::::::::::::::


