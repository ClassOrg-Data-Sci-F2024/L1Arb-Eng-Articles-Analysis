# Progress Reports

## 1st Progress Report

I have found the data that I am going to use for my analysis and managed to download the transcripts from [TalkBank](Vercellotti) as `.cha` files. The next step I had to take was read the data into R. This was challenging at first because I couldn't wrap my head around reading multiple files at once, but I managed to use `list.files()` to put the file paths into an object which allowed me to read each individual file in the list simply by using `map()` with `read_lines()`. After I successfully read the transcripts into R, it appeared that each line in a single one transcript was a separate character string. I assume if I will eventually put the data into a matrix form, each transcript needs to be one single character string. Perhaps this will allow me to extract matches more easily for my analysis. To turn each transcript into a single character string, I used `str_flatten()` coupled with `map()`(see the [pipeline](Data_pipeline.md) file for more details).

### Next steps

I realized after I had read the data into R that not all the speakers in the files are L1 Arabic speakers. So the first step is to go through the file that includes participants' IDs and manually check which files are not for L1 Arabic speakers. This will not remove too much data since the majority of speakers are L1 Arabic, according to Vercellotti (2017). Following that, I will find a way to create a data frame to house the transcripts which will help me extract matches for articles and article use contexts.

## 2nd Progress Report

So far, I have made sure that all the transcripts I am working with are provided by L1 Arabic speakers. I also cleaned the transcripts from metadata (information about the participant, the recording, language, etc.) and from `CHAT` formatting to prepare for extracting the relevant data from the transcripts. Following that, I created a data frame with participant IDs, levels, and their transcript and used that data frame to create three data frames based on level (low-intermediate, high-intermediate, low-advanced). These data frames were created in order to separately tag the POS using the `udpipe` package for each level and analyze the use of English articles for each level. To do this, I created a column in the output of the POS tagger in which each word in the text was tagged with POS (e.g. the/DT topic/NN). This allowed me to extract contexts of article use for the three levels. I am planning to have three data frames, one with correct use of indefinite articles, one with incorrect use of indefinite articles, and one no indefinite articles where one is necessary, see the code [pipeline](Data_pipeline.md) for more details.

### Sharing Scheme

I am using the raw data provided by Vercellotti, M. L. (2017) in TalkBank and will derive from it data for my analysis. TalkBank uses the Creative Commons BY-NC-SA 4.0 license which allows users of the data to "*copy and redistribute the material in any medium or format*" and to "*remix, transform, and build upon the material*" as long as they follow the [license](https://creativecommons.org/licenses/by-nc-sa/4.0/) terms. This means I can share the data as long as I follow the terms, which will help in understanding my coding process since a decent portion of it is dedicated to the texts.

### License

According to the terms of the Creative Commons [license](https://creativecommons.org/licenses/by-nc-sa/4.0/), "*If you remix, transform, or build upon the material, you must distribute your contributions under the same license as the original.*". This means that the license I choose must be the same as the one used by Vercellotti and TalkBank, specifically **CC BY-NC-SA 4.0**.

## 3rd Progress Report

I have created a list of mass nouns to tag the mass nouns in the data. This was necessery because the POS tagger does not mark mass nouns and tags them similarly to count singular nouns, which caused issues when extracting contexts of indefinite article use. Onec that was done, I extracted the target contexts for correct, incorrect, and null article use for low-intermediate, high-intermediate, and low-advanced participants. I then put the extracted contexts into a data frame for each level and added a column for the level and use category (correct, incorrect, null). This was to make it easier for me to put all the contexts from all three levels into a column and add two columns for level and use category. This final data frame will be used for the analysis. See the [pipeline](Data_pipeline) for details.

Since the goal is determine whether Arabic overtly marks indefiniteness, a count of the indefinite contexts should help answer this. If null has the most occurrences, than it is likely that Arabic does not overtly/morphologically mark indefiniteness.
