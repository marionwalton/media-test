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
A DTM represents a corpus as a large table (known as a matrix). 

We will study a small example of a document-term matrix, using this famous quotation from Nelson Mandela's 1964 speech during the Rivonia Trial: 

> "I have cherished the ideal of a democratic and free society in which all persons will live together in harmony and with equal opportunities. It is an idea for which I hope to live for and to see realized. But, my Lord, if it needs be, it is an ideal for which I am prepared to die."

In **quanteda** the function to create a DTM is `dfm()`, as it can be used for textual features other than individual words or tokens (e.g. emojis or punctuation marks). 

Each document has a separate row, each word has a separate column, and each cell has a number showing how often a particular word appears in a particular document.

We start by creating a list of the individual words in the sentences, which in **quanteda** uses the `tokens()` function. 
Our second step is to convert these words into a DTM using `dfm()`. For the purposes of this example, we will treat each sentence as a separate "text".



``` r
texts <- c(
      "I have cherished the ideal of a democratic and free society in which all persons will live together in harmony and with equal opportunities", 
"It is an idea for which I hope to live for and to see realized", 
"But my Lord if it needs be it is an ideal for which I am prepared to die")

d <- tokens(texts) %>%
  dfm()
```

We will take this DTM and look at its matrix structure, using the `convert()` function. 


``` r
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

The example matrix above shows that we have certainly lost a lot when we represent the closing sentences of Mandela's speech from the dock as a DTM. We have lost the original word order of the conclusion to his four hour long speech, which means we lose Mandela's poetic use of repetition and the carefully crafted drama of his final statement. 

When we represent the speech in a numeric matrix format we move away from the original sequence of the words are quantifying Mandela's linguistic choices so that we can see the patterns in the speech, which helps us to notice some of his rhetorical strategies, such as the repetition of the first person pronoun "I" and the words "ideal" and "live", as well as the poignant shift from the repeated positive terms "cherished", "democratic",  "free", "equal", "hope",  and harmony" to the determination of the final shocking negative, "die". 

Nonetheless, by converting the whole speech into the quantitative DTM format, we can more easily use computational methods to compare Mandela's 1964 speech to his other famous speeches, or to speeches made by other political leaders and statesmen.

Now we will load the full text of the speech from the `rivonia_speech_sentences.csv` to investigate these linguistic patterns.


``` r
# load file with video metadata (you tube data tools)
speech <- read_csv(
  here("data","rivonia_speech_sentences.csv"),
  na= "")
```

``` output
Rows: 487 Columns: 1
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (1): text

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Once we have loaded the full text of the speech we will need to use the `corpus()`, `tokens()` and `dfm()`functions to represent the speech as a DTM which will allow us to investigate its linguistic patterns.


``` r
d <- corpus(speech) %>%
  tokens(remove_punct=T) %>%
  dfm()
```

Using the `dim()` function we see that the resulting DFM has 487 sentences and 2154 tokens. The `head()` function tells us that it is a sparse matrix, (99.03% sparse). In other words, very few (0,97%) of the 2154 words are ever repeated. Even from the first few rows of the DTM, we can see that the most repeated words are the common words in English such as "I", "the", "a" and "in". Interpreting these relative frequencies requires us to understand an important concept in computational analysis of text, namely **frequency**.

## Frequency

Frequency is a key concept underpinning the analysis of text and corpora. As a purely quantitative measure, media researchers need to use word frequencies with a sensitivity to the word-distribution patterns in human languages, and the importance of context for meaning

# Frequency lists

-   Help direct researcher's investigations
-   Measures of dispersion can reveal trends across texts
-   Can provide a sociological profile of a given word or phrase

# Many potential problems

-   Can be reductive and generalising
-   Can oversimplify
-   Focus on differences between word distributions can obscure more interesting interpretations.

## Why is frequency important?

Language is not a random affair - it is *rule based*. Words are likely to occur predictably in relationship to other words. 

At the same time, human beings use language creatively and people can make many choices about how they want to use language. Language has *rule-generating* qualities which is why it changes over time and varies depending on the context where it is used. 

## Why are our linguistic choices important?

Media studies builds on the insights of discourse analysis and corpus linguistics, which have sensitised researchers to the ideological implications of people's linguistic choices. 

> "No terms are neutral. Choice of words expresses an ideological position". (Stubbs, 1996:107; Baker, 47)

> "If people speak or write in an unexpected way, or make one linguistic choice over another, more obvious one, then that reveals something about their intentions, whether conscious or not." (Baker:48)

## Charting frequencies with quanteda

Using the `textstat_frequency()` function and `ggplot()` we can chart the most frequently used 60 words (features) from Mandela's speech:


``` r
tstat_freq_d <- textstat_frequency(d, n = 60)

feature_freq <- ggplot(tstat_freq_d, aes(x = frequency, y = reorder(feature, frequency))) +
  geom_point() +
  labs(x = "Frequency", y = "Feature")
feature_freq
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-6-1.png" style="display: block; margin: auto;" />


## Grammatical/Function words

The chart shows us that the most commonly used words in the speech as a whole are similar to the common English words we saw repeated in the final sentences.These words are known as grammatical or function words and are the most commonly used in a language. They seldom change over short periods of time.

For this reason, grammatical and function words are on lists of words to be excluded from frequency counts. These lists are known as "stop words"

## Lexical words

By contrast, the frequencies of the lexical words in Mandela's speech ("African", "ANC", "Africans", "people", "political", "umkhonto", "Africa", "white", "South","violence", "government","policy","against","communist") show us what a specific text or corpus is about.

## Defining stopwords

Let us start by excluding common English words from our analysis:


``` r
## exclude common english words
mystopwords <- stopwords("english",
                         source="snowball")
```

## Removing stopwords

After removing stopwords from the DTM using the function `dfm_remove()` we can chart frequencies for the most common lexical words.


``` r
d <- d %>%
  dfm_remove(mystopwords)

tstat_freq_d <- textstat_frequency(d, n = 60)

feature_freq <- ggplot(tstat_freq_d, aes(x = frequency, y = reorder(feature, frequency))) +
  geom_point() +
  labs(x = "Frequency", y = "Feature")
feature_freq
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-8-1.png" style="display: block; margin: auto;" />

With the stop words excluded, the chart shows us more clearly what the speech was about.


:::::::::::::::::::::::::::::::::::::::: keypoints

- Use the **`quanteda`** package to analyse text data.
- Use `corpus()`, `tokens()`,`dfm()`, `dfm_remove()` and stopword lists to prepare text for analysis.
- Use `textstat_frequency` to investigate the most frequently used tokens or features in a dfm. 
- Plot frequencies using **`ggplot`** and the **`quanteda`** function `textplot_wordcloud`.

::::::::::::::::::::::::::::::::::::::::::::::::::


