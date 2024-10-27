# Progress Reports

## 1st Progress Report

I have found the data that I am going to use for my analysis. I managed to download the transcripts from TalkBank as `.cha` files. The next step I had to take was read the data into R. This was challenging at first because I couldn't wrap my head around reading multiple files at once, but I managed to use `list.files()` to put the file paths into an object which allowed me to read each individual file in the list simply by using `map()` with `read_lines()`. After I successfully read the transcripts into R, it appeared that each line in a single one transcript was a separate character string. I assume if I will eventually put the data into a matrix form, each transcript needs to be one single character string. Perhaps this will allow me to extract matches more easily for my analysis. To turn each transcript into a single character string, I used `str_flatten()` coupled with `map()`.

### Next steps

I realized after I had read the data into R that not all the speakers in the files are L1 Arabic speakers. So the first step is to go through the file that includes participants' IDs and manually check which files are not for L1 Arabic speakers. This will not remove too much data since the majority of speakers are L1 Arabic, according to Vercellotti (2017). Following that, I will find a way to create a data frame to house the transcripts which will help me extract matches for articles and article use contexts.
