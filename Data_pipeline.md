
# Data pipeline

``` r
# The transcripts I want to analyze are in .cha files, and each transcript is in a separate file. In order to be able to read all the .cha files at once, I first assigned the directory that includes them to an object.

speech <- "C:/Users/Abdul/OneDrive/Desktop/Data Science/L1Arb-Eng-Articles-Analysis/Vercellotti"



# To read all the files at once, I first have to read their individual paths in a list form and assign it to an object.

speech <- list.files(speech, pattern = "*.cha", full.names = TRUE)



# There are 293 files in this directory, however, some transcripts are not the output of L1 Arabic speakers. This will be resolved very soon and will leave enough data for the analysis since the majority are Arabic speakers. It is also important to note that each participant contributed more than one recording (3-6).

length(speech)
```

    ## [1] 293

``` r
# Now that I have all the file paths assigned to an object, I will use map() to apply read_lines() on each element of the list. read_lines() essentially reads the contents of the file.

speechlines <- map(speech, read_lines)


# To make each transcript one character string instead of multiple strings, I use str_flatten() here.

speechlines <- map(speechlines, str_flatten)
```

As I mentioned in the progress [report](progress_report.md), the next
step is to filter out irrelevant recordings and create a data frame
through which I can work on putting the data in the shape that I want.
Since the participants in this data contributed recordings across
different levels in their language program, the shape of the data I am
aiming for will likely have a participant’s transcript from a certain
level as a single observation, so each participant will be part of three
observation in the data frame, as low-intermediate, high-intermediate,
and low-advanced.
