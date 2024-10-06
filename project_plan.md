# Projec Plan

## Summary

The consensus is that Arabic does not have a morphological marker of indefiniteness (there are arguments for the opposite but, to my knowledge, no convincing evidence exists). There is work that analyzes English article use of Arabic-speaking learners. However, the data in these studies either analyze results from elicitation tasks given to learners or focus on corpus analysis of written production, very little work exists on spoken data analysis for article production from Arab learners of English. The plan for this project is to analyze the use of English indefinite articles by Arabic L1 learners in writing vs speaking. More specifically, do Arab learners of English have the same level of accuracy in their use of indefinite English articles in writing vs speaking?

## Data

To answer this question, I will use The University of Pittsburgh English Language Institute Corpus (PELIC). This corpus includes writing and (transcribed) speaking data for some learners, both of which will be utilized for the analysis. However, the speaking data exists in a different repository on TalkBank. The first step is to match learners' writing and speaking data using a key for their IDs in the writing and speaking datasets. This will determine the size of the data because there are much fewer data points in TalkBank than in the writing data in the PELIC repo. That said, the data on TalkBank present a few obstacles that need to be overcome before any data tidying and manipulation is done:

#### Potential Obstacle

-   The transcribed speaking data files in TalkBank are in .cha format. I will either need to learn how to use `vocaldia::read.cha()` to read this file type or find another method if `read.cha()` proves to be suboptimal.

-   Additionally, each transcript from TalkBank is in a separate .cha file. Is there a way to read them all at once? if not, how can they be put in one file to be read in Rstudio? these are important questions that need to be answered as soon as possible.

## Analysis

The end goal is to see if Arabic learners of English differ in their use of indefinite articles in speaking compared to writing. I hypothesize that there will be a significant difference in the use of NULL articles between speaking and writing because Arabic does not mark indefiniteness morphologically. English learners from languages that lack articles have been shown to fluctuate between the use of *the* and *a* in various contexts (details about this can be found in T Ionin, H Ko, K Wexler (2004), as well as literature on English article acquisition from articleless L1s). Arabic, on the other hand, possesses a definite article that behaves similarly to the definite article in English and, although it does not mark indefiniteness morphologically, indefiniteness is marked by the absence of an article in indefinite contexts. This is why I anticipate that NULL articles will be present more in spoken data because I believe spoken data represents subconscious knowledge of the language better. Therefore, the transfer of NULL article might be more perceivable since learners have less time to process their production and rely on their memories of explicit instruction they received, as they would while writing.

## References

Juffs, A., Han, N-R., & Naismith, B. (2020). The University of Pittsburgh English Language Corpus (PELIC) [Data set]. <http://doi.org/10.5281/zenodo.3991977>

Vercellotti, M. L. (2017). The development of complexity, accuracy, and fluency in second language performance: A longitudinal study. Applied Linguistics, 38(1), 90-111. (URL for spoken data by Vercellotti: <https://slabank.talkbank.org/access/English/Vercellotti.html>)
