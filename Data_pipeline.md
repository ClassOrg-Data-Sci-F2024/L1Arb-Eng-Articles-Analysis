
# Data pipeline

- *Existing*

First, I need to make sure all the files are of L1 Arabic speakers.

``` r
# read in the key which includes speakers' ids and L1s
id <- read_excel("Private/user_id-anon_id-key_EDITED.xlsm")
```

    ## Warning: Expecting numeric in A1315 / R1315C1: got 'tqn3"'

    ## Warning: Expecting numeric in B1315 / R1315C2: got 'Truong'

``` r
# only select the relevant columns
id <- id %>% 
  select(user_id, native_language)

# the lowest id value in TalkBank is 848 and the highest is 1351. This filters out participants who are not involved in the TalkBank data and only leaves the L1 Arabic.
Arbids <- id %>% 
  filter(user_id > 847, user_id < 1352, native_language == "Arabic")

# extract the ids as a pattern 
onlyarb <- str_extract(Arbids$user_id, "\\d{3,}")

# this is the file that includes the TalkBank transcripts
speech <- "data/Vercellotti"

# this reads all the file paths to an object from which I can filter non-Arabic L1s
speech <- list.files(speech, pattern = "*.cha", full.names = TRUE)

#  collapse the different strings (ids) into one string separated by | for matching
onlyarb <- str_flatten(onlyarb, "|")
 
# filter out non-Arabic L1s
speech <- str_subset(speech, onlyarb)
```

I need to create a variable for participants’ ids, and a variable for
their level.

``` r
#this gives the ids listed in the file names
ids <- str_extract(speech, "\\d{3,}")

# this gives the level listed in the file names
levels <- str_extract(speech, "_\\d{1}[A-Z]")
levels <- str_remove_all(levels, "_|[A-Z]") 

# changing the names in level to something more meaningful
levels <- str_replace_all(levels, c("3" = "low-int", "4" = "high-int", "5" = "low-adv"))
```

Before I create the data frame, I need to remove the first few lines of
metadata in the transcripts

``` r
# read the lines 
lines <- map(speech, read_lines)

# put the maximum number of lines in an object
max <- map_int(lines, length) %>% max()

# this is mapping the rep function over every element in the list. The number of NAs added to each line is calculated by the max number of line - the number of lines of each element.
evenlines <- map(lines, ~c(.x, rep(NA, max - length(.x))))

# now that all the elements have even number of lines, I can put it in a tibble to use slice to remove lines I don't want. 
tibblelines <- as_tibble(evenlines, .name_repair = "unique")
```

    ## New names:
    ## • `` -> `...1`
    ## • `` -> `...2`
    ## • `` -> `...3`
    ## • `` -> `...4`
    ## • `` -> `...5`
    ## • `` -> `...6`
    ## • `` -> `...7`
    ## • `` -> `...8`
    ## • `` -> `...9`
    ## • `` -> `...10`
    ## • `` -> `...11`
    ## • `` -> `...12`
    ## • `` -> `...13`
    ## • `` -> `...14`
    ## • `` -> `...15`
    ## • `` -> `...16`
    ## • `` -> `...17`
    ## • `` -> `...18`
    ## • `` -> `...19`
    ## • `` -> `...20`
    ## • `` -> `...21`
    ## • `` -> `...22`
    ## • `` -> `...23`
    ## • `` -> `...24`
    ## • `` -> `...25`
    ## • `` -> `...26`
    ## • `` -> `...27`
    ## • `` -> `...28`
    ## • `` -> `...29`
    ## • `` -> `...30`
    ## • `` -> `...31`
    ## • `` -> `...32`
    ## • `` -> `...33`
    ## • `` -> `...34`
    ## • `` -> `...35`
    ## • `` -> `...36`
    ## • `` -> `...37`
    ## • `` -> `...38`
    ## • `` -> `...39`
    ## • `` -> `...40`
    ## • `` -> `...41`
    ## • `` -> `...42`
    ## • `` -> `...43`
    ## • `` -> `...44`
    ## • `` -> `...45`
    ## • `` -> `...46`
    ## • `` -> `...47`
    ## • `` -> `...48`
    ## • `` -> `...49`
    ## • `` -> `...50`
    ## • `` -> `...51`
    ## • `` -> `...52`
    ## • `` -> `...53`
    ## • `` -> `...54`
    ## • `` -> `...55`
    ## • `` -> `...56`
    ## • `` -> `...57`
    ## • `` -> `...58`
    ## • `` -> `...59`
    ## • `` -> `...60`
    ## • `` -> `...61`
    ## • `` -> `...62`
    ## • `` -> `...63`
    ## • `` -> `...64`
    ## • `` -> `...65`
    ## • `` -> `...66`
    ## • `` -> `...67`
    ## • `` -> `...68`
    ## • `` -> `...69`
    ## • `` -> `...70`
    ## • `` -> `...71`
    ## • `` -> `...72`
    ## • `` -> `...73`
    ## • `` -> `...74`
    ## • `` -> `...75`
    ## • `` -> `...76`
    ## • `` -> `...77`
    ## • `` -> `...78`
    ## • `` -> `...79`
    ## • `` -> `...80`
    ## • `` -> `...81`
    ## • `` -> `...82`
    ## • `` -> `...83`
    ## • `` -> `...84`
    ## • `` -> `...85`
    ## • `` -> `...86`
    ## • `` -> `...87`
    ## • `` -> `...88`
    ## • `` -> `...89`
    ## • `` -> `...90`
    ## • `` -> `...91`
    ## • `` -> `...92`
    ## • `` -> `...93`
    ## • `` -> `...94`
    ## • `` -> `...95`
    ## • `` -> `...96`
    ## • `` -> `...97`
    ## • `` -> `...98`
    ## • `` -> `...99`
    ## • `` -> `...100`
    ## • `` -> `...101`
    ## • `` -> `...102`
    ## • `` -> `...103`
    ## • `` -> `...104`
    ## • `` -> `...105`
    ## • `` -> `...106`
    ## • `` -> `...107`
    ## • `` -> `...108`
    ## • `` -> `...109`
    ## • `` -> `...110`
    ## • `` -> `...111`
    ## • `` -> `...112`
    ## • `` -> `...113`
    ## • `` -> `...114`
    ## • `` -> `...115`
    ## • `` -> `...116`
    ## • `` -> `...117`
    ## • `` -> `...118`
    ## • `` -> `...119`
    ## • `` -> `...120`
    ## • `` -> `...121`
    ## • `` -> `...122`
    ## • `` -> `...123`
    ## • `` -> `...124`
    ## • `` -> `...125`
    ## • `` -> `...126`
    ## • `` -> `...127`
    ## • `` -> `...128`
    ## • `` -> `...129`
    ## • `` -> `...130`
    ## • `` -> `...131`
    ## • `` -> `...132`
    ## • `` -> `...133`
    ## • `` -> `...134`
    ## • `` -> `...135`
    ## • `` -> `...136`
    ## • `` -> `...137`
    ## • `` -> `...138`
    ## • `` -> `...139`
    ## • `` -> `...140`
    ## • `` -> `...141`
    ## • `` -> `...142`
    ## • `` -> `...143`
    ## • `` -> `...144`
    ## • `` -> `...145`
    ## • `` -> `...146`
    ## • `` -> `...147`
    ## • `` -> `...148`
    ## • `` -> `...149`
    ## • `` -> `...150`
    ## • `` -> `...151`
    ## • `` -> `...152`
    ## • `` -> `...153`
    ## • `` -> `...154`
    ## • `` -> `...155`
    ## • `` -> `...156`
    ## • `` -> `...157`
    ## • `` -> `...158`
    ## • `` -> `...159`
    ## • `` -> `...160`
    ## • `` -> `...161`
    ## • `` -> `...162`
    ## • `` -> `...163`
    ## • `` -> `...164`
    ## • `` -> `...165`
    ## • `` -> `...166`
    ## • `` -> `...167`
    ## • `` -> `...168`
    ## • `` -> `...169`
    ## • `` -> `...170`
    ## • `` -> `...171`
    ## • `` -> `...172`
    ## • `` -> `...173`
    ## • `` -> `...174`
    ## • `` -> `...175`
    ## • `` -> `...176`
    ## • `` -> `...177`
    ## • `` -> `...178`
    ## • `` -> `...179`
    ## • `` -> `...180`
    ## • `` -> `...181`
    ## • `` -> `...182`
    ## • `` -> `...183`
    ## • `` -> `...184`
    ## • `` -> `...185`
    ## • `` -> `...186`
    ## • `` -> `...187`
    ## • `` -> `...188`
    ## • `` -> `...189`
    ## • `` -> `...190`
    ## • `` -> `...191`
    ## • `` -> `...192`
    ## • `` -> `...193`
    ## • `` -> `...194`
    ## • `` -> `...195`
    ## • `` -> `...196`
    ## • `` -> `...197`
    ## • `` -> `...198`
    ## • `` -> `...199`
    ## • `` -> `...200`
    ## • `` -> `...201`
    ## • `` -> `...202`
    ## • `` -> `...203`
    ## • `` -> `...204`
    ## • `` -> `...205`
    ## • `` -> `...206`
    ## • `` -> `...207`
    ## • `` -> `...208`

``` r
# this removes the first few lines. For some reason slice_tail selects from the start not the end, and slice_head selects from the end.
removedlines <- slice_tail(tibblelines, n = -9)

# save the sliced transcripts as a list in an object to put them in a tibble with ids and levels later.
removedlines <- as.list(removedlines) 
```

creating the data frame

``` r
# create the data frame/tibble with ids and sliced transcripts
speechdf <- tibble(ID = ids, Level = levels, Transcript = removedlines)
```

Cleaning the texts from `.cha` formatting.

``` r
speechdf$Transcript <- speechdf$Transcript %>% 
  str_remove_all("\\bNA\\b|\\d+_\\d+|\\*\\d+|\\d{3}|@...|\\\\t|\\\n|c\\(|[^\\w|\\s]|\\buh\\s|\\bah\\s|\\bPAR|\\bPAR0") %>% 
  str_replace_all("_", " ")
```

    ## Warning in stri_replace_all_regex(string, pattern,
    ## fix_replacement(replacement), : argument is not an atomic vector; coercing

``` r
head(speechdf$Transcript)
```

    ## [1] "descib my topic is describe your favorite meal from your childhood   we know as s a young  young child my favorite food was or or  favorite meal was hamburgers   I like hamburgers because its usually the fast food that I ate all the time   so the ingodu ingredients it was two pieces of bread a beef a lettuce tomatoes an onion and a little bit of ketchup and mayonnaise   hamburgers for me its the special  mea special main dish   so usually my mum  my mum advise me s n that she said to me that its very bad and  and not good for health   but usually she made it for me in the home hamburger as at any restaurant that they made hamburger that I like   ham  its  eating fast food its  its a bad benefit because its  its bad for health and  and bad for health   and its will make for you some trouble in your body   so my  so its  ver its  signi its very significant to  to  to stop eating this kind of food   and my father try to assist me to stop eating this food   many restaurant they are available to  to  to make any kind of hamburgers   so Im trying to  to  to stop eating this fas fast food so e                                                "                                                                                                                                                                                               
    ## [2] "a the topic is transportation   in this world a lot of countries have a different economies   so today I want to talk about the tra transportation between my country and Pittsburgh   Pittsburgh had a good way to pick up people by buses   so there is a lot of available buses in a specific time   its  and  and they are capable to pick up people from anywhere   so in my country we dont have a lot of buses   because m its maybe  we have  no plans  to  to make  a in my country we dont have a good plan to  to  to help a company that consist buses   so  so my country should create or process a good plan to had  to have a lot of buses to pick up people and  and  and flourish in this na                                                    "                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            
    ## [3] "okay  the topic is someone I admire   okay  Ill talk today about my father   my father is a  mam my father born in Jeddah Saudi Arabia   and I admire with my father because hes intelligent man and polite man   and he like his family and his children   and he like his job   he  actually hes the founder of his company   so my father treat me in a good way   and he wish for me everything good   and I wish in the future that someday that I can became same like my father   my father live in the Riyadh o with our family  3 syllables   and  and my father gave me everything that I want   because that reason I like him   and he always assist me and support me   and he always cont cont contribute with po poor people   and my father hes a significan significant person in my life   and he  he teach me everything in appropriate way   and in the future I want be same my father because hes everything in my life and without my father Im nothing   so I wish him all the best   and I wish him longer age and everything so thank you                                         "                                                                                                                                                                                                                                                                                  
    ## [4] "the topic is um talking about a problem in the world that concerned me   actually  theralot there is a lot  actually there is a lot of problem that concerns me and concerns everybody   but I  wan today I want talk on one specific problem which is in this world there is a lot of cars   cars causes pollution in the air   ah specifically the exhaust of the car exhaust a lot of chemicals or embalms in the in the  in the airs   so  so Im looking forward that if they can solve this problem   they can solve it by  by they sell less cars in the world that they put a specific amount for people that they can buy a car   for example a lot of Saudis country or any country just one family they has more than six or seven cars   that cause a problem  that causing problem with the with the  with the pollution   so  so obviously that will  will  wil it will  it  il obviously it will have result by  its  by pollution   and there is  theres a lot of aspects that can affect the people or affect the air   so Im looking forward for solve this problem   there is aks a approximate  each house in any country approximately f ten or fifteen cars for the  for just one family   so  so tha they  they must decide a goal to achieve it to solve this problem   they hafta solve this problem as soon as they can                                              "
    ## [5] "the topic is talk about something I regret that I have done   first yesterday the teacher was dispu distributing the  the worksheet or or the  or the exam paper  eh paper test and actually I didnt study   and  and while Im facing the the the  the exam I  I didnt know how to answer   so  so the result was that I failed in the exam   so if  if I studied good I  I shouldnt fail in the in  th in the exam   so I should  have  um what I should have done differently I should have to study hard and worked hard to pass this exam   and  um and if I study  if I studied good and if I work hard I would not fail in my exam   I will get full mark   and  um I  will  and I will pass the exam thank you   so when I  there is some process I have to follow for studying to pass any exam to get a full mark                                                   "                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
    ## [6] "um Im going talk about violence in the tv show   m my opinion I  th violence on tv shouldnt be banned   ah because these days tv generate a lot of movies involve va violence I  killing everything   even  even though cartoons they also generate violence   so I think k our ch children get useta it and also the adults   so its  it is  are not big deal for children or any kind of age   um of course watching tv is very crutral crucial for  for any person whether in its including a violence or not including any violence   um um so tv used  they sometimes glorify that there is two kind of cartoons or two kinds of shows that for the adults and for the children   so also children in the  in the  in the between age  between age they  they know how to distinguish between these k the cartoon and also movies   and also the families teach  teach them how to know the difference between it   so in my opinion it shouldnt ish  it shouldnt be banned because we needta watch tv and  thats all                                                  "

I am going to create 3 data frames to analyze the article use for each
level

``` r
lowint <- speechdf %>% 
  filter(Level == "low-int")

highint <- speechdf %>% 
  filter(Level == "high-int")

lowadv <- speechdf %>% 
  filter(Level == "low-adv")
```

I will POS tag the data frames to make analyzing the contexts of article
use possible

``` r
library(udpipe)

# downloading the model only needs to be done once.
udpipe_download_model(language = "english-ewt")
```

    ## Downloading udpipe model from https://raw.githubusercontent.com/jwijffels/udpipe.models.ud.2.5/master/inst/udpipe-ud-2.5-191206/english-ewt-ud-2.5-191206.udpipe to C:/Users/Abdul/OneDrive/Desktop/Data Science/L1Arb-Eng-Articles-Analysis/english-ewt-ud-2.5-191206.udpipe

    ##  - This model has been trained on version 2.5 of data from https://universaldependencies.org

    ##  - The model is distributed under the CC-BY-SA-NC license: https://creativecommons.org/licenses/by-nc-sa/4.0

    ##  - Visit https://github.com/jwijffels/udpipe.models.ud.2.5 for model license details.

    ##  - For a list of all models and their licenses (most models you can download with this package have either a CC-BY-SA or a CC-BY-SA-NC license) read the documentation at ?udpipe_download_model. For building your own models: visit the documentation by typing vignette('udpipe-train', package = 'udpipe')

    ## Downloading finished, model stored at 'C:/Users/Abdul/OneDrive/Desktop/Data Science/L1Arb-Eng-Articles-Analysis/english-ewt-ud-2.5-191206.udpipe'

    ##      language
    ## 1 english-ewt
    ##                                                                                                  file_model
    ## 1 C:/Users/Abdul/OneDrive/Desktop/Data Science/L1Arb-Eng-Articles-Analysis/english-ewt-ud-2.5-191206.udpipe
    ##                                                                                                                                  url
    ## 1 https://raw.githubusercontent.com/jwijffels/udpipe.models.ud.2.5/master/inst/udpipe-ud-2.5-191206/english-ewt-ud-2.5-191206.udpipe
    ##   download_failed download_message
    ## 1           FALSE               OK

``` r
#loading the language model
model <- udpipe_load_model(file = ("english-ewt-ud-2.5-191206.udpipe"))
```

<!-- instead of creating three different df create a column with the tagged dfs -->
<!-- ```{r} -->
<!-- ANNOTspeech <- speechdf %>% -->
<!--   mutate(annoTXT = map(Transcript, ~ udpipe_annotate(model, .x) %>% as_tibble)) -->
<!-- low_int <-  low_int %>% mutate(annotTXT = paste(low_int$token, "/", low_int$xpos, collapse = " ", sep = ""))  -->
<!-- # saved the file so I don't have to run udpipe_annotate() every time -->
<!-- saveRDS(ANNOTspeech, "articles-analysis.csv") -->
<!-- ANNOTspeech <- readRDS("articles-analysis.csv") -->
<!-- head(ANNOTspeech$annoTXT, 1) -->
<!-- ``` -->

POS tagging the data frames.

``` r
# low_int <- udpipe_annotate(model, x = lowint$Transcript) %>% 
#   as_tibble()
```

``` r
# high_int <- udpipe_annotate(model, x = highint$Transcript) %>% 
#   as_tibble()
```

``` r
# low_adv <-  udpipe_annotate(model, x = lowadv$Transcript) %>% 
#   as_tibble()
```

a list of common nouns:

``` r
commonNN <- c("admiration/NNC", "advice/NNC", "air/NNC", "anger/NNC", "anticipation/NNC", 
              "assistance/NNC", "awareness/NNC", "bacon/NNC", "baggage/NNC", "blood/NNC", 
              "bravery/NNC", "chess/NNC", "clay/NNC", "clothing/NNC", "coal/NNC", "compliance/NNC", 
              "comprehension/NNC", "confusion/NNC", "consciousness/NNC", "cream/NNC", "darkness/NNC", 
              "diligence/NNC", "dust/NNC", "education/NNC", "empathy/NNC", "enthusiasm/NNC", "envy/NNC", 
              "equality/NNC", "equipment/NNC", "evidence/NNC", "feedback/NNC", "fitness/NNC", "flattery/NNC", 
              "foliage/NNC", "fun/NNC", "furniture/NNC", "garbage/NNC", "gold/NNC", "gossip/NNC", 
              "grammar/NNC", "gratitude/NNC", "gravel/NNC", "guilt/NNC", "happiness/NNC", "hardware/NNC", 
              "hate/NNC", "hay/NNC", "health/NNC", "heat/NNC", "help/NNC", "hesitation/NNC", "homework/NNC", 
              "honesty/NNC", "honor/NNC", "hospitality/NNC", "hostility/NNC", "humanity/NNC", "humility/NNC", 
              "ice/NNC", "immortality/NNC", "independence/NNC", "information/NNC", "integrity/NNC", 
              "intimidation/NNC", "jargon/NNC", "jealousy/NNC", "jewelry/NNC", "justice/NNC", "knowledge/NNC", 
              "literacy/NNC", "logic/NNC", "luck/NNC", "lumber/NNC", "luggage/NNC", "mail/NNC", "management/NNC", 
              "merchandise/NNC", "milk/NNC", "morale/NNC", "mud/NNC", "music/NNC", "nonsense/NNC", "oppression/NNC", 
              "optimism/NNC", "oxygen/NNC", "participation/NNC", "pay/NNC", "peace/NNC", "perseverance/NNC", 
              "pessimism/NNC", "pneumonia/NNC", "poetry/NNC", "police/NNC", "pride/NNC", "privacy/NNC", 
              "propaganda/NNC", "public/NNC", "punctuation/NNC", "recovery/NNC", "rice/NNC", "rust/NNC", 
              "satisfaction/NNC", "shame/NNC", "sheep/NNC", "slang/NNC", "software/NNC", "spaghetti/NNC", 
              "stamina/NNC", "starvation/NNC", "steam/NNC", "steel/NNC", "stuff/NNC", "support/NNC", "sweat/NNC", 
              "thunder/NNC", "timber/NNC", "toil/NNC", "traffic/NNC", "training/NNC", "trash/NNC", 
              "understanding/NNC", "valor/NNC", "vehemence/NNC", "violence/NNC", "warmth/NNC", "waste/NNC", 
              "weather/NNC", "wheat/NNC", "wisdom/NNC", "work/NNC", "food/NNC", "ham/NNC", "beef/NNC", "lettuce/NNC", "tv/NNC")
```

Now, I will annotate the texts with POS tags so I can analyze the
contexts in addition to tagging mass nouns, which the tagger does not
mark.

``` r
#I'll annotate the texts with pos
# low_int <-  low_int %>% mutate(annotTXT = paste(low_int$token, "/", low_int$xpos, collapse = " ", sep = "")) 

# this pattern was created to replace /NN with /NNC for mass nouns  
patternn <- paste0("\\b(", paste(sub("/NNC", "", commonNN), collapse = "|"), ")\\b/NN")

# Replace /NN with /NNC to tag mass nouns
# low_int$annotTXT <- str_replace_all(low_int$annotTXT, patternn, "\\1/NNC")
```

``` r
# high_int <-  high_int %>% mutate(annotTXT = paste(high_int$token, "/", high_int$xpos, collapse = " ", sep = ""))
# Replace /NN with /NNC to tag mass nouns
# high_int$annotTXT <- str_replace_all(high_int$annotTXT, patternn, "\\1/NNC")
```

``` r
 # low_adv <-  low_adv %>% mutate(annotTXT = paste(low_adv$token, "/", low_adv$xpos, collapse = " ", sep = ""))

 # low_adv$annotTXT <- str_replace_all(low_adv$annotTXT, patternn, "\\1/NNC")
```

I have saved each tagged df so that I don’t have to run the tagging
operations every time. Below these chunks, I have set the code for
loading. Feel free to use that to skip running the tagger.

``` r
# saveRDS(low_int, "data/lowint.csv")
# saveRDS(high_int, "data/highint.csv")
# saveRDS(low_adv, "data/lowadv.csv")
```

instead of tagging every time, i made these tagged dfs.

``` r
low_int <- readRDS("data/lowint.csv")
high_int <- readRDS("data/highint.csv")
low_adv <- readRDS("data/lowadv.csv")
```

Here, I will attempt to extract the contexts of article use for
low-intermediate, high-intermediate, and low-advanced.

Low-intermediate

``` r
LI_correct <- low_int$annotTXT %>% 
  str_unique() %>% 
  str_remove_all("\\b../NN") %>% 
  str_extract_all("(a|an)/DT\\s(\\w+/\\w+\\s)?\\w+/NN\\s") %>% 
  unlist()



# I still need to find a list for common mass nouns because it is incorrect to a/an with mass nouns also, but the tagger does not tag them
LI_incorrect <- low_int$annotTXT %>% 
  str_unique() %>% 
  str_remove_all("\\b../NN") %>% 
  str_extract_all("(a|an)/DT\\s(\\w+/\\w+\\s)?\\w+/(NNS|NNC)") %>% 
  unlist()



# I created a pattern to exclude so that I can focus on cases where indefinite articles were not used. 

pattern <- "\\w+/(DT|CD)\\s(\\w+/\\w+\\s)?(\\w+|someone|something|somewhere|everything|everyone)/NN\\s"
patt <- "\\w+/VB.\\s(\\w+/\\w+\\s)?(someone|something|somewhere|everything|everyone|anyone|anybody|anthing)/NN\\s"

# pulls out instances where a/an should have been used.
LI_null <- low_int$annotTXT %>%
  str_unique() %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_split(pattern) %>%
  str_split(patt) %>% 
  str_remove_all("um/NN") %>%
  str_extract_all("\\w+/\\w+\\s\\w+/VB.\\s(\\w+/\\w+\\s)?\\w+/NN\\s") %>% 
  unlist()
```

    ## Warning in stri_split_regex(string, pattern, n = n, simplify = simplify, :
    ## argument is not an atomic vector; coercing

    ## Warning in stri_replace_all_regex(string, pattern,
    ## fix_replacement(replacement), : argument is not an atomic vector; coercing

High-intermediate

``` r
HI_correct <- high_int$annotTXT %>% 
  str_unique() %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_extract_all("(a|an)/DT\\s(\\w+/\\w+\\s)?\\w+/NN\\s") %>% 
  unlist()



HI_incorrect<- high_int$annotTXT %>% 
  str_unique() %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_extract_all("(a|an)/DT\\s(\\w+/\\w+\\s)?\\w+/(NNS|NNC)") %>% 
  unlist()



pattern <- "\\w+/(DT|CD)\\s(\\w+/\\w+\\s)?\\w+/NN\\s"
patt <- "\\w+/VB.\\s(\\w+/\\w+\\s)?(someone|something|somewhere|everything|everyone|anyone|anybody)/NN\\s"

HI_null <- high_int$annotTXT %>%
  str_unique() %>% 
  str_split(pattern) %>%
  str_split(patt) %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_remove_all("\\bPAR/NN|\\bPAR0/NN|\\bPAR0") %>% 
  str_extract_all("\\w+/VB.\\s(\\w+/\\w+\\s){0,2}\\w+/NN\\s") %>% 
  unlist()
```

    ## Warning in stri_split_regex(string, pattern, n = n, simplify = simplify, :
    ## argument is not an atomic vector; coercing

    ## Warning in stri_replace_all_regex(string, pattern,
    ## fix_replacement(replacement), : argument is not an atomic vector; coercing

Low-advanced

``` r
LA_correct <- low_adv$annotTXT %>% 
  str_unique() %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_extract_all("(a|an)/DT\\s(\\w+/\\w+\\s)?\\w+/NN\\s") %>% 
  unlist()



LA_incorrect <- low_adv$annotTXT %>% 
  str_unique() %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_extract_all("(a|an)/DT\\s(\\w+/\\w+\\s)?\\w+/(NNS|NNC)") %>% 
  unlist()

  

LA_null <- low_adv$annotTXT %>% 
  str_unique() %>% 
  str_split(pattern) %>%
  str_split(patt) %>% 
  str_remove_all("\\b../NN\\s") %>% 
  str_remove_all("\\b./NN\\s") %>% 
  str_extract_all("\\w+/VB.\\s(\\w+/\\w+\\s){0,2}\\w+/NN\\s") %>% 
  unlist()
```

    ## Warning in stri_split_regex(string, pattern, n = n, simplify = simplify, :
    ## argument is not an atomic vector; coercing

    ## Warning in stri_replace_all_regex(string, pattern,
    ## fix_replacement(replacement), : argument is not an atomic vector; coercing

Here, I put the extracted contexts into a df and added a columns tagging
level and use.

low-intermediate

``` r
lowint_corr <- str_unique(LI_correct) %>% 
  as_tibble() %>% 
rename("LI_correct" = "value") %>% 
  mutate(status = "low-int_correct", .after = LI_correct)
lowint_corr
```

    ## # A tibble: 68 × 2
    ##    LI_correct                  status         
    ##    <chr>                       <chr>          
    ##  1 "an/DT onion/NN "           low-int_correct
    ##  2 "a/DT little/JJ bit/NN "    low-int_correct
    ##  3 "a/DT bad/JJ benefit/NN "   low-int_correct
    ##  4 "a/DT the/DT topic/NN "     low-int_correct
    ##  5 "a/DT lot/NN "              low-int_correct
    ##  6 "a/DT good/JJ way/NN "      low-int_correct
    ##  7 "a/DT specific/JJ time/NN " low-int_correct
    ##  8 "a/DT good/JJ plan/NN "     low-int_correct
    ##  9 "a/DT company/NN "          low-int_correct
    ## 10 "a/DT mam/NN "              low-int_correct
    ## # ℹ 58 more rows

``` r
lowint_incorr <- str_unique(LI_incorrect) %>%
  as_tibble() %>% 
rename("LI_incorrect" = "value") %>% 
  mutate(status = "low-int_incorrect", .after = LI_incorrect)



lowint_null <- str_unique(LI_null) %>% 
  as_tibble() %>% 
rename("LI_null" = "value") %>% 
  mutate(status = "low-int_null", .after = LI_null)
lowint_null
```

    ## # A tibble: 84 × 2
    ##    LI_null                                     status      
    ##    <chr>                                       <chr>       
    ##  1 "because/IN hes/VBZ intelligent/JJ man/NN " low-int_null
    ##  2 "I/PRP m/VBP nothing/NN "                   low-int_null
    ##  3 "I/PRP was/VBD childhood/NN cereal/NN "     low-int_null
    ##  4 "people/NNS use/VBP taxi/NN "               low-int_null
    ##  5 "it/PRP s/VBZ good/JJ transportation/NN "   low-int_null
    ##  6 "people/NNS use/VBP car/NN "                low-int_null
    ##  7 "he/PRP was/VBD child/NN "                  low-int_null
    ##  8 "he/PRP had/VBD many/JJ problem/NN "        low-int_null
    ##  9 "mother/NN took/VBD care/NN "               low-int_null
    ## 10 "he/PRP took/VBD care/NN "                  low-int_null
    ## # ℹ 74 more rows

high-intermediate

``` r
highint_corr <- str_unique(HI_correct) %>% 
  as_tibble() %>% 
rename("HI_correct" = "value") %>% 
  mutate(status = "high-int_correct", .after = HI_correct) 
highint_corr 
```

    ## # A tibble: 235 × 2
    ##    HI_correct                    status          
    ##    <chr>                         <chr>           
    ##  1 "a/DT problem/NN "            high-int_correct
    ##  2 "a/DT lot/NN "                high-int_correct
    ##  3 "a/DT specific/JJ amount/NN " high-int_correct
    ##  4 "a/DT car/NN "                high-int_correct
    ##  5 "a/DT goal/NN "               high-int_correct
    ##  6 "a/DT full/JJ mark/NN "       high-int_correct
    ##  7 "a/DT computer/NN "           high-int_correct
    ##  8 "a/DT good/JJ company/NN "    high-int_correct
    ##  9 "a/DT good/JJ job/NN "        high-int_correct
    ## 10 "a/DT lot/NN place/NN "       high-int_correct
    ## # ℹ 225 more rows

``` r
highint_incorr <- str_unique(HI_incorrect) %>%
  as_tibble() %>% 
rename("HI_incorrect" = "value") %>% 
  mutate(status = "high-int_incorrect", .after = HI_incorrect)


highint_null <- str_unique(HI_null) %>% 
  as_tibble() %>% 
rename("HI_null" = "value") %>% 
  mutate(status = "high-int_null", .after = HI_null)
```

low-advanced

``` r
lowad_corr <- str_unique(LA_correct) %>%
  as_tibble() %>% 
rename("LA_correct" = "value") %>% 
  mutate(status = "low-adv_correct", .after = LA_correct)
```

``` r
lowad_incorr <- str_unique(LA_incorrect) %>%
  as_tibble() %>% 
rename("LA_incorrect" = "value") %>% 
  mutate(status = "low-adv_incorrect", .after = LA_incorrect)
```

``` r
lowad_null <- str_unique(LA_null) %>% 
  as_tibble() %>% 
rename("LA_null" = "value") %>% 
  mutate(status = "low-adv_null", .after = LA_null)
```

Creating the variables from the tibbles created above to get the “final”
form of the data from which the analysis can be done.

``` r
Status <- c(lowint_corr$status, lowint_incorr$status, lowint_null$status, highint_corr$status, highint_incorr$status, highint_null$status, lowad_corr$status, lowad_incorr$status, lowad_null$status)

Article_contexts <- c(lowint_corr$LI_correct, lowint_incorr$LI_incorrect, lowint_null$LI_null, highint_corr$HI_correct, highint_incorr$HI_incorrect,  highint_null$HI_null, lowad_corr$LA_correct, lowad_incorr$LA_incorrect,  lowad_null$LA_null)

articles <- tibble(Article_contexts, Status)

# separating level and correctness of use into two columns
articles <- articles %>% 
  separate(Status, into = c("Level", "Use"), sep = "_")


#This is the final form of the data. For any new changes I make I'll run this again to overwrite the old version
saveRDS(articles, "data/articles_final.csv")
```

For this project the main goal is to see how many times the participants
used articles, correctly or incorrectly, vs omitted them from obligatory
contexts. To this end, a count of instances will be used. Some more work
needs to be done to ensure all the examples included in each category is
appropriate, but the analysis goal will remain the same for now.

``` r
articles %>% 
  count(Level, Use)
```

    ## # A tibble: 9 × 3
    ##   Level    Use           n
    ##   <chr>    <chr>     <int>
    ## 1 high-int correct     235
    ## 2 high-int incorrect    16
    ## 3 high-int null        259
    ## 4 low-adv  correct      64
    ## 5 low-adv  incorrect    12
    ## 6 low-adv  null         70
    ## 7 low-int  correct      68
    ## 8 low-int  incorrect    12
    ## 9 low-int  null         84
