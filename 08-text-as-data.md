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

- Introduce basic concepts needed to use the **`quanteda`** package for discourse analysis: corpus, tokens, dfm,and frequency.
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

Computational linguistics and Corpus Linguistics are two related areas of study in linguistics. Both of these areas provide approaches which media scholars can use when analysing textual data in media texts.

Computational linguistics is a broad inter-disciplinary area of study where software and algorithms are developed to analyse and synthesise language and speech for applications such as machine translation, speech recognition, machine learning and deep learning ("AI"). Corpus linguistics has developed methods to study trends and patterns in language use by analysing large collections of electronically stored, naturally occurring texts. Both of these areas are related to Natural Language Processing (NLP) which is a subfield of computer science.

While Computational Linguistics has a strongly quantitative focus, Corpus Linguistics often includes qualitative analysis (such as examining concordance lines).  Corpus linguistics involves much qualitative work interpreting text, and so can be used to extend the scope of traditional media studies approaches to linguistic discourse such as Critical Discourse Analysis (CDA).

For more 

(Baker et al 2008:273)

## What is a Corpus?

A *corpus* is a set of documents which stores large quantities of real-life text. The plural form of the word is *corpora*.

You can find a set of South African language corpora on the [SADILAR corpus portal](https://corpus.sadilar.org/corpusportal/explore/corpus) website.

## Using a Document-Term Matrix

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

The example matrix above shows the losses and the gains when we represent the closing sentences of Mandela's four hour long speech from the dock as a DTM. 

We lose the original word order of the conclusion to the speech, its poetic use of repetition and the impact of the final statement. 

At the same time, we gain other insights. Since we represent the speech in a numeric matrix format Mandela's linguistic choices are quantified and we can see certain lexical patterns. This draws attention to some rhetorical strategies, such as the repetition of the first person pronoun "I" and the repeated words "ideal" and "live", which lead up to the shock of the final emphatic "die". 

Furthermore, by converting the whole speech into the quantitative DTM format, we can more easily use computational methods to compare Mandela's 1964 speech to other famous speeches, such as his speech in 1994, or to speeches made by other political leaders and statesmen.

Now we will load the full text of the speech from a collection of speeches `speeches.tsv` to investigate these linguistic patterns.


``` r
# load file with text of political speeches
speeches <- read_tsv(
  here("data","speeches.tsv"),
  na= ""
)
```

``` output
Rows: 2 Columns: 7
── Column specification ────────────────────────────────────────────────────────
Delimiter: "\t"
chr (7): first_name, president, date, delivery, type, party, text

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Now that we have loaded the collection we can select only the Rivonia speech using `filter()`. 

Then we will use the `corpus()`, `tokens()` and `dfm()`functions to represent the speech as a DTM. 


``` r
# select only the Rivonia speech, tokenize and convert to a DTM

d <- speeches %>%
  filter(date == "April 20 1964" ) %>%
  corpus() %>%
  tokens(remove_punct=T) %>%
  dfm()
```
Once the speech is converted to a DTM, we can use other functions to explore its linguistic patterns. 


``` r
head(d)
```

``` output
Document-feature matrix of: 1 document, 2,154 features (0.00% sparse) and 6 docvars.
       features
docs      i am the first accused hold   a bachelor's degree  in
  text1 179 12 751     9       1    2 171          1      1 264
[ reached max_nfeat ... 2,144 more features ]
```
The `head()` function tells us that the resulting DFM has 2154 tokens. It is a sparse matrix, (0.00% sparse). In other words, very few of the 2154 words are ever repeated. 

The function `topfeatures()` highlights the most commonly used words in the speech. Unsurprisingly, these are common words in English such as "I", "the", "a" and "in".


``` r
topfeatures(d)
```

``` output
 the   of   to  and   in    i    a that  was this 
 751  461  387  339  264  179  171  152  140  107 
```

Making sense of such frequencies requires us to understand an important concept in computational analysis of text, namely **frequency**.

# Using Word Frequencies to Analyse Text

Frequency is a key concept underpinning the analysis of text and corpora. As a purely quantitative measure, media researchers need to use word frequencies with a sensitivity to the word-distribution patterns in human languages, and the importance of context for meaning

Word frequency lists are an important starting point and can help direct an investigation. We combine them with measures of dispersion, which are used to reveal trends across texts. For example, certain linguistic patterns may occur more often at the beginning or ends of texts.

At the same time, frequencies can be reductive and generalising. If we're interested in meaning it's important not to oversimplify, and focusing only on word distributions can obscure more interesting interpretations of a text.

# Why is frequency important?

Language is not a random affair - it is *rule based*. Words are likely to occur predictably in relationship to other words. 

At the same time, human beings use language creatively and people can make many choices about how they want to use language. Language has *rule-generating* qualities which is why it changes over time and varies depending on the context where it is used. 

# The relationship between frequency and ideology

Media studies builds on the insights of discourse analysis and corpus linguistics, which have sensitised researchers to the ideological implications of the words people choose to use.


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

<img src="fig/08-text-as-data-rendered-unnamed-chunk-8-1.png" style="display: block; margin: auto;" />


## Grammatical/Function words

The chart shows us that the most commonly used words in the speech as a whole are similar to the common English words we saw repeated in the final sentences.These words are known as grammatical or function words and are the most commonly used in a language. They seldom change over short periods of time.

For this reason, grammatical and function words are on lists of words to be excluded from frequency counts. These lists are known as "stop words"

## Lexical words

By contrast, the frequencies of the lexical words in Mandela's speech ("African", "ANC", "Africans", "people", "political", "umkhonto", "Africa", "white", "South","violence", "government","policy","against","communist") show us what this specific text or corpus is about.

## Defining stopwords

We can start identifying these important lexical words by excluding common English words from our analysis:


``` r
## exclude common english words
mystopwords <- stopwords("english",
                         source="snowball")
```

## Removing stopwords

After removing stopwords from the DTM using the function `dfm_remove()` we can chart frequencies for the most common lexical words used in the Rivonia speech.


``` r
d <- d %>%
  dfm_remove(mystopwords)

tstat_freq_d <- textstat_frequency(d, n = 60)

feature_freq <- ggplot(tstat_freq_d, aes(x = frequency, y = reorder(feature, frequency))) +
  geom_point() +
  labs(x = "Frequency", y = "Feature")
feature_freq
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-10-1.png" style="display: block; margin: auto;" />

With the stop words excluded, the chart shows us more clearly what the speech was about.

## Wordclouds and visualisation

Wordclouds are a popular visualisation format for frequencies because they allow us to focus on meanings.


``` r
topfeatures(d)
```

``` output
  african       anc  africans    people political  umkhonto    africa     white 
       71        57        55        48        43        38        35        35 
    south  violence 
       34        33 
```

``` r
textplot_wordcloud(d, max_words=100)
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-11-1.png" style="display: block; margin: auto;" />



``` output
Loading required package: wordcloud2
```

``` output
Loading required package: RColorBrewer
```

<!--html_preserve--><div class="wordcloud2 html-widget html-fill-item" id="htmlwidget-7118ef4a92a18da8c069" style="width:504px;height:504px;"></div>
<script type="application/json" data-for="htmlwidget-7118ef4a92a18da8c069">{"x":{"word":["first","accused","hold","bachelor's","degree","arts","practised","attorney","johannesburg","number","years","partnership","oliver","tambo","convicted","prisoner","serving","five","leaving","country","without","permit","inciting","people","go","strike","end","may","1961","outset","want","say","suggestion","made","state","opening","struggle","south","africa","influence","foreigners","communists","wholly","incorrect","done","whatever","individual","leader","experience","proudly","felt","african","background","outsider","might","said","youth","transkei","listened","elders","tribe","telling","stories","old","days","amongst","tales","related","wars","fought","ancestors","defence","fatherland","names","dingane","bambata","hintsa","makana","squngthi","dalasile","moshoeshoe","sekhukhuni","praised","glory","entire","nation","hoped","life","offer","opportunity","serve","make","humble","contribution","freedom","motivated","relation","charges","case","must","deal","immediately","length","question","violence","things","far","told","court","true","untrue","however","deny","planned","sabotage","plan","spirit","recklessness","love","result","calm","sober","assessment","political","situation","arisen","many","tyranny","exploitation","oppression","whites","admit","one","persons","helped","form","umkhonto","sizwe","played","prominent","role","affairs","arrested","august","1962","statement","shall","correct","certain","false","impressions","created","witnesses","demonstrate","acts","referred","evidence","committed","also","relationship","national","congress","part","personally","organizations","communist","party","order","explain","matters","properly","set","achieve","methods","prescribed","achievement","objects","chosen","became","involved","activities","responsible","clearly","fell","outside","policy","organisation","charged","indictment","us","know","justification","authorized","refer","briefly","roots","organization","already","mentioned","others","started","two","reasons","firstly","believed","government","become","inevitable","unless","leadership","given","canalize","control","feelings","outbreaks","terrorism","produce","intensity","bitterness","hostility","various","races","produced","even","war","secondly","way","open","succeed","principle","white","supremacy","lawful","modes","expressing","opposition","closed","legislation","placed","position","either","accept","permanent","inferiority","defy","chose","law","broke","avoided","recourse","legislated","resorted","show","force","crush","policies","decide","answer","adopt","formed","members","behind","anc","tradition","non-violence","negotiation","means","solving","disputes","believe","belongs","live","group","black","interracial","tried","avoid","last","minute","doubt","seen","whole","history","bears","subsequently","describe","tactics","decided","therefore","something","1912","defend","rights","seriously","curtailed","act","threatened","native","land","thirty-seven","1949","adhered","strictly","constitutional","put","forward","demands","resolutions","sent","delegations","belief","grievances","settled","peaceful","discussion","africans","advance","gradually","full","governments","remained","unmoved","less","instead","becoming","greater","words","chief","lutuli","president","1952","later","awarded","nobel","peace","prize","thirty","spent","knocking","vain","patiently","moderately","modestly","barred","door","fruits","moderation","past","greatest","laws","restricting","progress","today","reached","stage","almost","determined","time","change","protest","employed","embodied","decision","taken","apartheid","unlawful","demonstrations","pursuant","launched","defiance","campaign","charge","volunteers","based","principles","passive","resistance","8,500","defied","went","jail","yet","single","instance","course","defier","nineteen","colleagues","organizing","sentences","suspended","mainly","judge","found","discipline","stressed","throughout","volunteer","section","established","word","amadelakufa","used","asked","take","pledge","uphold","dealing","pledges","introduced","completely","context","soldiers","army","pledged","fight","civil","dedicated","workers","prepared","lead","campaigns","initiated","distribute","leaflets","organize","strikes","particular","required","called","face","penalties","imprisonment","whipping","now","legislature","public","safety","criminal","amendment","passed","statutes","provided","harsher","offences","protests","despite","continued","1956","156","leading","alliance","including","high","treason","suppression","communism","non-violent","issue","gave","judgement","acquitted","counts","included","count","sought","place","existing","regime","always","label","opponents","allegation","repeated","present","never","1960","shooting","sharpeville","resulted","proclamation","emergency","declaration","careful","consideration","obey","decree","governed","universal","human","basis","authority","banning","equivalent","accepting","silencing","refused","dissolve","underground","duty","preserve","built","fifty","unremitting","toil","self-respecting","disband","declared","illegal","held","referendum","led","establishment","republic","constituted","approximately","70","per","cent","population","entitled","vote","consulted","proposed","apprehensive","future","resolution","all-in","conference","call","convention","mass","eve","unwanted","failed","attended","persuasions","secretary","undertook","stay-at-home","coincide","person","arrest","consequently","leave","home","family","practice","hiding","accordance","demonstration","instructions","organizers","government's","introduce","new","mobilize","armed","forces","send","saracens","vehicles","townships","massive","designed","intimidate","indication","rule","alone","milestone","road","appear","irrelevant","trial","fact","none","hope","enable","appreciate","attitude","eventually","adopted","bodies","concerned","liberation","movement","dominant","idea","loss","still","1963","return","june","leaders","give","implied","threat","action","continue","anything","else","abject","surrender","problem","whether","stood","non-racial","democracy","shrank","drive","apart","hard","facts","brought","nothing","repressive","fewer","easy","understand","long","talking","day","man","win","back","nevertheless","prevailed","upon","pursue","discussed","denied","nonracial","achieved","followers","beginning","lose","confidence","developing","disturbing","ideas","forgotten","feature","scene","1957","women","zeerust","ordered","carry","passes","1958","enforcement","cattle","culling","sekhukhuniland","1959","cato","manor","protested","pass","raids","attempted","impose","bantu","authorities","pondoland","thirty-nine","died","disturbances","riots","warmbaths","seething","unrest","disturbance","pointed","growth","among","showed","uses","maintain","teaches","oppressed","use","oppose","small","groups","urban","areas","spontaneously","making","plans","violent","forms","arose","danger","well","directed","particularly","type","engendered","places","increasingly","taking","though","prompted","strife","conducted","anxious","came","conclusion","unrealistic","wrong","preaching","met","easily","arrived","channels","embark","desired","solely","left","choice","manifesto","published","16","december","exhibit","ad","comes","remain","choices","submit","come","hit","power","feeling","press","can","morally","obliged","consult","spoke","wish","phase","objectives","clear","view","summarized","follows","function","fulfil","joined","express","undertake","turn","body","closely","knit","politically","ceasing","essential","activity","propaganda","permissible","nature","hand","described","depart","fifty-year-old","extent","longer","disapprove","controlled","hence","subject","disciplinary","times","guidance","different","contemplated","consent","tell","november","took","formulated","heritage","racial","harmony","much","drifting","towards","blacks","viewed","alarm","mean","destruction","difficult","ever","examples","results","scars","disappear","eradicate","inter-racial","great","sides","avoidance","dominated","thinking","realized","prospect","account","formulating","flexible","permitted","needs","recognized","resort","wanted","ready","four","possible","guerrilla","warfare","revolution","method","exhaust","light","logical","involve","offered","best","race","relations","kept","minimum","bore","fruit","democratic","reality","bloodshed","clash","late","hour","actions","awaken","everyone","realization","disastrous","nationalist","bring","supporters","senses","changed","reach","desperate","initial","analysis","economic","depended","large","foreign","capital","trade","plants","interference","rail","telephone","communications","tend","scare","away","goods","industrial","seaports","schedule","run","heavy","drain","thus","compelling","voters","reconsider","attacks","lines","linked","buildings","symbols","source","inspiration","addition","provide","outlet","urging","adoption","concrete","proof","stronger","line","fighting","successfully","organized","reprisals","sympathy","cause","roused","countries","pressure","bear","perform","strict","right","start","injure","kill","planning","carrying","operations","mr","x","z","command","powers","co-option","appoint","regional","commands","targets","training","finance","direction","local","within","framework","laid","select","attacked","beyond","endangered","fit","overall","forbidden","operation","incidentally","terms","importation","jewish","irgun","zvai","leumi","operated","israel","1944","1948","port","elizabeth","durban","selection","intended","attack","selected","congregated","empty","stations","work","isolated","connection","claimed","issued","commenced","response","characteristically","strong","stand","firm","ignore","respond","suggesting","responded","laager","contrast","encouragement","suddenly","happening","eager","news","enthusiasm","generated","successes","began","speculate","soon","obtained","weighed","anxiety","drawn","moving","separate","camps","prospects","avoiding","newspapers","carried","reports","punished","death","keep","scores","friction","1920","famous","masabala","twenty-four","gathered","demand","release","killed","police","civilians","1921","hundred","bulhoek","affair","1924","administrator","south-west","rebelled","imposition","dog","tax","1","1950","eighteen","shootings","21","march","sixty-nine","unarmed","sharpevilles","terror","happen","cost","rest","happened","together","problems","faced","decisions","convinced","rebellion","limitless","opportunities","indiscriminate","slaughter","precisely","soil","drenched","blood","innocent","preparations","long-term","undertaking","favourable","least","risk","provision","possibility","undergo","compulsory","military","build","nucleus","trained","men","able","prepare","proper","necessary","administration","professions","equipped","participate","allowed","attend","pan-african","central","east","southern","early","addis","ababa","need","preparation","tour","states","obtaining","facilities","solicit","scholarships","higher","education","matriculated","fields","changes","administrators","willing","administer","note","proceed","delegate","success","wherever","promises","help","united","london","received","gaitskell","grimond","promised","support","julius","nyerere","tanganyika","kawawa","prime","minister","emperor","haile","selassie","ethiopia","general","abboud","sudan","habib","bourguiba","tunisia","ben","bella","algeria","modibo","keita","mali","leopold","senghor","senegal","sekou","toure","guinea","tubman","liberia","milton","obote","uganda","invited","visit","oujda","headquarters","algerian","diary","exhibits","study","art","whilst","abroad","underwent","share","hazards","notes","lectures","contained","summaries","books","strategy","admitted","documents","writing","acknowledge","studies","equip","play","drifted","approached","every","objective","see","examine","types","west","going","classic","clausewitz","covering","variety","mao","tse","tung","che","guevara","writings","anglo-boer","merely","read","contain","personal","views","arrangements","recruits","impossible","scheme","co-operation","offices","permission","departure","original","applied","batch","actually","passing","returned","reported","trip","little","alteration","save","penalty","cautiously","possibilities","exhausted","expressed","premature","recorded","document","r","14","ahead","sufficient","value","allegations","revert","occurrences","referring","bombing","private","houses","pro-government","september","october","provocation","accepted","conspiracy","commit","explained","externally","overlapping","functions","internally","difference","atmosphere","committee","room","difficulties","arise","field","practical","affected","bannings","house","arrests","individuals","capacities","blurred","distinction","abolished","care","distinct","prior","recruiting","trying","object","recruited","served","like","solomon","mbanjwa","officers","exception","respective","committees","bennett","mashiyana","reginald","ndubi","hear","meetings","another","rivonia","knew","reason","presently","following","manner","indicated","april","entailed","travelling","living","villages","cities","second","half","year","visiting","parktown","arthur","goldreich","meet","privately","although","direct","association","known","socially","since","informed","town","thereafter","arranged","michael","harmel","naturally","ideal","lived","outlaw","compelled","indoors","daytime","venture","cover","darkness","liliesleaf","farm","differently","efficiently","obvious","disguise","assumed","fictitious","name","david","moved","stayed","11","january","july","natal","5","neither","officials","governing","connected","numerous","occasions","stay","executive","nhc","elsewhere","staying","frequently","visited","main","paid","visits","discussions","subjects","ideological","questions","generally","experiences","soldier","palmach","wing","haganah","palestine","got","recommended","knowledge","aims","assume","try","argue","marxism","disproved","reared","head","creed","nationalism","concept","cry","sea","stands","fulfilment","important","charter","blueprint","socialist","calls","redistribution","nationalization","provides","mines","banks","monopoly","industry","big","monopolies","owned","domination","perpetuated","spread","hollow","gesture","repeal","gold","prohibitions","european","companies","respect","anc's","corresponds","programme","economy","enterprise","fresh","prosperous","classes","middle","class","period","advocated","revolutionary","structure","recollection","condemned","capitalist","society","correctly","short","term","solution","regards","unlike","goal","unity","party's","aim","remove","capitalists","replace","working-class","emphasize","distinctions","seeks","harmonize","vital","often","close","common","removal","complete","community","interests","world","similar","perhaps","striking","illustration","britain","america","soviet","union","hitler","nobody","dared","suggest","turned","churchill","roosevelt","tools","working","shortly","occurred","openly","active","colonial","short-term","correspond","movements","struggles","malaya","indonesia","similarly","sprung","europe","chiang","kai-shek","bitterest","enemies","ruling","assumption","china","1930s","pattern","non-communists","joint","involving","provincial","albert","nzula","former","moses","kotane","j","b","marks","member","younger","admitting","existed","specific","issues","watering","league","expulsion","proposal","heavily","defeated","voted","conservative","sections","opinion","defended","ground","inception","school","thought","parliament","accommodating","convictions","won","point","upheld","ingrained","prejudice","experienced","politicians","readily","friends","theoretical","differences","luxury","afford","decades","treat","beings","equals","eat","talk","attainment","stake","equate","supported","brands","exponents","bans","named","pernicious","banned","imprisoned","internal","politics","international","aid","nations","councils","bloc","afro-asian","colonialism","seems","sympathetic","plight","western","condemnation","speaks","louder","voice","circumstances","brash","young","politician","proclaim","think","exactly","beliefs","regarded","patriot","born","umtata","forty-six","ago","guardian","cousin","acting","paramount","tembuland","sabata","dalindyebo","kaizer","matanzima","attracted","classless","attraction","springs","marxist","reading","admiration","societies","production","belonged","rich","poor","stated","influenced","independent","widely","gandhi","nehru","nkrumah","nasser","socialism","catch","advanced","overcome","legacy","extreme","poverty","marxists","indeed","debate","basic","task","moment","discrimination","furthers","welcome","assistance","realize","literature","conversations","gained","impression","regard","parliamentary","system","undemocratic","reactionary","contrary","admirer","magna","carta","petition","bill","veneration","democrats","british","institutions","country's","justice","institution","independence","impartiality","judiciary","fail","arouse","american","doctrine","separation","arouses","sentiments","feel","search","formula","absolutely","impartial","tie","free","borrow","financial","financed","sources","funds","raised","whenever","special","example","events","slender","resources","scale","hampered","lack","raise","add","discovered","attained","well-known","anti-communists","recommendation","confine","mission","urgently","needed","liberty","disclose","playing","imaginary","enrol","ostensibly","truth","preposterous","join","real","hardships","language","prosecutor","so-called","basically","features","hallmarks","entrenched","seek","repealed","dignity","agitators","teach","richest","extremes","remarkable","contrasts","enjoy","highest","standard","misery","forty","hopelessly","overcrowded","cases","drought-stricken","reserves","erosion","overworking","makes","labourers","labour","tenants","squatters","farms","conditions","serfs","ages","30","towns","developed","social","habits","closer","respects","standards","impoverished","low","incomes","highest-paid","actual","latest","figures","25","1964","carr","manager","non-european","department","datum","average","according","carr's","r42.84","month","monthly","wage","r32.24","46","families","earn","enough","goes","malnutrition","disease","incidence","deficiency","diseases","tuberculosis","pellagra","kwashiorkor","gastro-enteritis","scurvy","health","infant","mortality","medical","officer","pretoria","kills","58,491","destroy","organs","retarded","mental","initiative","reduce","concentration","secondary","affect","performed","complaint","ways","break","formal","worker","acquiring","skill","wages","avenues","advancement","deliberately","hamper","coming","stop","subsidies","feeding","children","schools","supplement","diet","cruel","virtually","parents","receive","pay","schooling","quoted","institute","journal","40","age","seven","fourteen","vastly","afforded","1960-61","capita","spending","students","state-aided","estimated","r12.46","cape","province","available","r144.57","wealthier","homes","quality","educational","5,660","junior","certificate","362","matric","presumably","consistent","1953","reform","natives","taught","childhood","equality","europeans","desirable","teachers","controls","fitted","chance","obstacle","colour-bar","better","jobs","reserved","moreover","obtain","employment","unskilled","semi-skilled","occupations","unions","recognition","conciliation","collective","bargaining","better-paid","successive","demonstrated","civilized","sheltered","grade","exceed","earnings","employee","answers","critics","saying","economically","inhabitants","comparison","cost-of-living","index","prevented","altering","imbalance","implies","entrenches","notion","menial","tasks","invariably","cleaned","look","around","sort","breed","emotions","fall","wives","money","feed","clothe","house-boy","garden-boy","labourer","hated","bits","render","liable","surveillance","male","brush","hundreds","thousands","thrown","worse","husband","wife","breakdown","effects","wander","streets","alive","leads","moral","alarming","rise","illegitimacy","growing","erupts","everywhere","dangerous","somebody","stabbed","assaulted","afraid","walk","dark","housebreakings","robberies","increasing","sentence","imposed","cure","festering","sore","capable","declares","o","endorsed","area","rented","confined","ghettoes","forced","unnatural","existence","men's","hostels","menfolk","permanently","widowed","eleven","o'clock","night","rooms","travel","bureau","tells","just","security","equal","disabilities","sounds","majority","fear","guarantee","enfranchisement","division","colour","entirely","artificial","disappears","century","racialism","triumphs","truly","inspired","suffering","lifetime","cherished","die"],"freq":[9,1,2,1,1,1,1,1,6,4,15,1,1,1,3,1,1,2,2,24,12,1,1,48,9,4,2,9,14,1,21,7,3,14,19,1,20,34,35,1,1,20,1,2,3,4,1,3,3,1,11,71,2,1,5,9,2,3,1,1,2,1,1,3,3,9,1,2,1,6,1,2,1,2,1,1,1,1,1,1,1,1,1,1,1,2,1,19,2,1,2,10,1,1,19,1,1,2,7,9,8,2,1,4,33,4,8,3,7,8,1,6,3,3,14,6,1,1,2,9,1,1,2,43,8,2,12,1,2,2,14,1,24,9,2,9,38,4,8,1,9,4,3,2,8,2,7,2,10,2,1,2,2,2,9,3,5,4,14,2,21,11,10,1,9,27,21,3,4,2,6,2,7,4,3,1,4,2,6,1,7,4,2,1,2,31,1,1,3,21,8,2,1,1,1,1,15,12,1,2,5,7,2,1,4,33,5,5,1,2,6,1,3,1,1,6,1,1,3,1,5,4,3,11,20,1,8,5,2,3,35,6,1,1,1,2,2,8,2,6,2,4,2,2,2,3,4,1,2,2,1,1,4,11,1,2,1,2,5,8,14,1,57,1,9,1,9,1,1,5,1,16,9,5,1,2,6,3,1,6,2,5,5,1,3,1,2,9,2,1,1,2,13,1,2,11,2,4,8,1,3,2,2,3,2,1,3,1,2,1,3,2,1,7,2,55,1,1,4,2,4,1,2,2,2,3,2,6,1,9,1,5,1,1,4,1,3,4,1,1,1,1,1,2,1,1,1,2,1,10,1,1,5,2,9,4,3,17,9,3,2,1,9,7,4,2,2,1,1,3,9,3,5,5,3,1,2,1,1,8,4,4,3,3,6,1,1,5,3,2,1,1,1,7,1,2,3,2,2,1,1,1,2,1,8,1,1,1,1,2,2,1,4,4,1,16,13,2,5,8,3,2,1,1,1,4,3,5,4,3,2,2,1,1,12,2,1,1,1,1,3,1,2,2,2,2,3,1,1,1,2,2,4,8,3,2,6,1,1,2,1,1,1,1,2,6,6,1,1,11,1,1,4,2,6,6,4,1,2,1,1,1,3,3,1,1,1,1,2,4,2,6,2,1,1,1,1,1,4,2,3,2,3,1,1,1,1,1,3,8,1,5,2,5,2,2,1,11,6,4,1,1,1,2,1,6,2,1,5,5,2,6,1,1,3,2,1,3,2,2,1,2,3,2,2,3,7,2,2,2,1,4,1,1,2,4,1,3,1,3,1,1,6,1,3,1,1,3,2,1,1,1,3,3,13,3,8,4,1,3,2,4,3,5,6,6,1,2,4,2,2,4,4,7,3,1,2,5,4,3,2,1,1,1,8,2,2,2,1,2,2,1,1,2,4,1,2,1,4,5,1,6,5,2,5,1,1,5,1,2,3,1,1,2,3,1,1,1,2,1,1,1,2,1,2,2,1,2,1,2,1,1,1,2,1,1,1,1,5,1,2,1,4,1,2,1,5,1,1,1,1,1,1,1,1,3,2,1,1,1,1,5,1,3,4,2,5,1,1,4,4,4,1,1,7,3,1,2,1,2,1,2,3,1,1,3,1,5,2,1,1,1,3,1,2,1,2,1,1,5,3,4,1,4,4,4,2,1,1,1,2,2,1,6,2,1,9,1,3,1,1,6,2,1,3,8,1,1,1,1,2,1,3,3,4,1,1,2,1,2,4,1,1,2,5,2,1,1,3,2,1,4,1,3,1,3,1,6,1,1,1,2,1,1,1,6,4,3,1,2,2,1,1,2,3,3,8,2,3,2,1,1,1,6,2,1,1,2,2,1,2,1,1,2,2,1,1,3,1,1,1,8,7,2,1,1,1,1,1,2,4,4,2,1,1,1,1,5,2,1,1,3,1,2,1,1,2,1,3,4,3,1,1,2,2,2,1,6,2,1,3,3,2,1,1,1,1,1,3,1,2,1,3,1,1,2,2,1,4,1,2,1,2,2,1,3,1,1,2,2,2,1,1,1,2,4,1,2,5,1,2,1,3,3,1,16,1,1,2,1,3,2,1,1,1,3,2,12,3,2,7,4,1,1,5,3,4,10,1,1,2,1,2,2,1,2,2,1,1,1,1,2,1,2,1,2,1,1,1,1,1,2,1,3,3,1,1,1,1,1,1,1,1,20,1,1,1,1,1,3,1,2,5,1,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,2,2,1,1,2,2,2,1,2,1,1,4,1,1,5,5,1,1,1,1,1,1,1,1,1,2,5,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1,1,2,1,1,3,1,3,3,2,1,1,1,1,1,2,1,1,2,3,1,1,1,3,2,1,1,1,1,1,1,2,2,6,3,3,3,5,3,1,1,5,1,1,1,2,7,3,1,2,5,1,4,2,2,3,1,2,6,1,2,1,1,3,10,1,2,1,1,1,1,1,1,1,1,1,1,1,4,2,6,1,1,2,8,1,1,3,1,3,4,1,1,1,1,6,1,1,1,1,1,2,2,3,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1,3,1,1,3,1,1,5,6,1,2,1,2,1,1,2,2,1,2,2,1,2,1,1,2,1,1,2,2,2,1,1,5,2,1,1,2,2,1,1,1,1,1,1,1,2,1,1,2,1,1,3,2,1,8,1,1,2,2,1,1,1,1,2,2,1,2,1,1,1,1,1,1,2,1,1,2,1,1,1,1,1,4,1,1,1,1,2,2,1,1,2,1,2,1,1,1,1,2,1,1,1,1,3,2,1,1,2,2,1,1,2,1,2,1,1,3,1,1,1,2,1,1,1,2,3,4,1,1,1,2,1,2,1,1,1,1,1,3,5,4,1,2,1,1,1,2,1,1,1,6,1,1,2,2,3,1,1,5,6,1,1,5,2,1,1,1,3,2,1,1,1,1,1,1,4,2,1,1,1,1,1,1,1,4,5,1,1,2,1,1,1,2,1,2,1,1,2,1,1,1,1,1,1,1,2,1,1,2,1,1,1,1,1,4,2,1,2,1,2,1,2,1,1,1,1,2,1,1,1,2,1,1,1,1,2,1,1,3,2,4,3,1,1,2,1,4,6,1,3,1,1,5,1,3,1,1,4,1,1,2,5,1,1,1,1,1,3,1,1,1,2,1,1,2,1,1,1,2,1,2,4,1,1,2,2,1,1,1,6,1,1,1,2,1,1,3,1,1,1,1,1,1,1,1,1,1,1,2,3,2,2,2,1,3,1,11,5,2,1,1,2,2,1,1,2,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,4,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,2,1,1,1,1,1,3,1,1,1,1,1,3,1,1,1,2,1,1,1,1,2,1,1,1,1,2,1,1,1,1,7,2,2,1,1,1,1,1,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,2,2,1,3,1,1,1,1,1,1,1,2,1,1,1,1,1,2,1,1,1,2,1,3,1,1,1,1,2,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,1,2,2,1,1,1,1,1,1,1,1,3,2,2,1,1,1,3,5,2,2,1,1,1,1,1,1,2,1,1,1,1,1,7,2,1,2,1,2,1,2,1,1,5,3,1,1,1,1,4,1,4,1,1,1,1,1,1,1,2,1,1,2,1,2,1,1,3,1,2,1,1,1,1,1,1,1,1,2,1,1,1,1,2,1,3,1,2,4,1,1,1,1,1,1,1,1,1,4,2,1,2,1,1,1,1,1,2,1,1,1,1,1,2,1,1,1,1,1,1,2,1,1,3,1,2,1,1,2,1,2,1,1,2,1,1,1,1,2,2,1,2,1,1,2,1,2,1,1,2,2,3,1,1,1,3,1,1,1,1,1,1,1,1,1,3,1,1,1,1,1,1,4,1,1,1,1,1,3,1,3,4,1,1,1,1,2,1,1,3,2,2,2,2,1,2,1,2,2,1,1,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1,2,2,1,1,1,1,1,1,2,1,2,1,1,1,1,1,1,14,3,1,1,1,1,3,1,1,1,1,1,2,1,1,1,1,1,1,1,2,2,1,1,1,2,1,1,2,2,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1,1,2,1,1,1,1,1,1,1,1,2,2,1,1,2,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,3,1,1,1,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,2,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,3,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1,1,1,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1,1,1,2,1,1,1,2,1,1,1,1,1,1,1,1,1,1,1,1],"fontFamily":"Segoe UI","fontWeight":"bold","color":["#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#9ECAE1","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#3182BD","#08519C","#3182BD","#6BAED6","#6BAED6","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#C6DBEF","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#3182BD","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#6BAED6","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#9ECAE1","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#9ECAE1","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#3182BD","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C","#08519C"],"minSize":0,"weightFactor":2.535211267605634,"backgroundColor":"white","gridSize":0,"minRotation":0,"maxRotation":0,"shuffle":true,"rotateRatio":0.4,"shape":"circle","ellipticity":0.65,"figBase64":null,"hover":null},"evals":[],"jsHooks":{"render":[{"code":"function(el,x){\n                        console.log(123);\n                        if(!iii){\n                          window.location.reload();\n                          iii = False;\n\n                        }\n  }","data":null}]}}</script><!--/html_preserve-->

Can you think of some of the disadvantages of representing data in this way?

Activity

Repeat these steps with the inaugural speech given by Mandela when he became President on 10 May 1994.


``` r
d <- speeches %>%
  filter(date == "10 May 1994" ) %>%
  corpus() %>%
  tokens(remove_punct=T) %>%
  dfm()  %>%
  dfm_remove(mystopwords)

topfeatures(d)
```

``` output
     us   world  people country   peace   human   south     let freedom   never 
      9       8       8       6       6       5       5       5       4       4 
```

``` r
textplot_wordcloud(d, max_words=100)
```

<img src="fig/08-text-as-data-rendered-unnamed-chunk-13-1.png" style="display: block; margin: auto;" />

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Which YouTube video categories are used to describe the videos in the sample? (Use the video_category_label column.) 
Create a tibble of these categories and the number of videos in each of them, arranged in descending order.

:::::::::::::::  solution

## Solution


``` r
videos %>%
   count(video_category_label, sort=TRUE) 
```

``` error
Error in eval(expr, envir, enclos): object 'videos' not found
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

``` error
Error in eval(expr, envir, enclos): object 'videos' not found
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

``` error
Error in eval(expr, envir, enclos): object 'videos' not found
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::: keypoints

- Use the **`quanteda`** package to analyse text data.
- Use `corpus()`, `tokens()`,`dfm()`, `dfm_remove()` and stopword lists to prepare text for analysis.
- Use `textstat_frequency` to investigate the most frequently used tokens or features in a dfm. 
- Plot frequencies using **`ggplot`** and the **`quanteda`** function `textplot_wordcloud`.

::::::::::::::::::::::::::::::::::::::::::::::::::


