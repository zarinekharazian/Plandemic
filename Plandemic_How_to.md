Walkthrough: Tracking the spread of the debunked Plandemic video
================
Zarine Kharazian, DFRLab
June 28, 2020

Despite efforts by major platforms to limit its spread, copies of the
widely debunked conspiracy video “Plandemic” continued to multiply and
spread largely through niche online conspiracy communities in early May
2020.

The DFRLab used the CrowdTangle API and an R package called
[CooRNet](http://coornet.org/index.html), developed by Fabio Giglietto,
Nicola Righetti, and Luca Rossi, to [track the spread of the viral
conspiracy](https://medium.com/dfrlab/why-the-debunked-covid-19-conspiracy-video-plandemic-wont-go-away-c9dd36c2037c)
through hundreds of Facebook groups.

This document walks through the data analysis portion of the research,
providing reproducible code for the key visualizations.

## Pulling in the data from CrowdTangle

The first step was to get a dataset from CrowdTangle of posts promoting
the Plandemic conspiracy shared to public Faebook groups that also
contained URLs. The goal here was to capture posts that linked to either
a copy of the video hosted off of Facebook, such as on YouTube or
dedicated domains, *or* to other content that furthered the conspiracy
(blog posts, op-eds, etc).

We created a search for posts containing the Plandemic video in
CrowdTangle. Then, we used the CrowdTangle Historical Data feature to
get all of the posts from the saved search containing links that were
posted between May 3, 2020 - May 10, 2020.

## Detecting rapid link-sharing using CooRNet

`CooRNet` is an R package that detects “coordinated link sharing
behavior,” which it defines as when public Facebook entities, such as
pages and groups, repeatedly share the same links within an unusually
short period of time from each other.

What constitutes an “unusually short period” of time is defined by the
“coordination interval,” which CooRNet calculates algorithmically. The
rationale of using this measure as a proxy for coordination is that it
would be unlikely that different Facebook entities would share the same
links as one another within that unusually short period of time on a
*repeated basis.*

In this analysis, we were not as interested in capturing coordination as
we were in mapping the rapid spread of the Plandemic conspiracy through
Facebook groups. The below analysis, therefore, is not necessarily
evidence of coordination on the part of the disparate Facebook assets;
rather, it suggests a pattern of rapid link-sharing related to Plandemic
throughout hundreds of different conspiracy communities, demonstrating
the conspiracy’s crossover appeal and the shared dynamics among these
communities.

We started out by following the tutorial available on the [CoorNet
site](http://coornet.org/tutorial01.html) to extract a list of entities
engaged in rapid linksharing. This series of steps, especially calling
`get_cthares`, will take a long time, as it queries the CrowdTangle API.
Be patient, maybe make yourself a cup of tea (or 11).

``` r
#From the tutorial: 

urls <- get_urls_from_ct_histdata(ct_histdata_csv="/Users/zkharazian/Downloads/2020-05-09-21-40-09-EDT-Historical-Report-plandemic-2020-05-03--2020-05-10.csv")

ctshares <- get_ctshares(urls, "url", "date", clean_urls = TRUE)

output <- get_coord_shares(ctshares, parallel = TRUE, clean_urls = TRUE, keep_ourl_only = TRUE)

get_outputs(output, ct_shares_marked.df = TRUE, highly_connected_g = TRUE, highly_connected_coordinated_entities = TRUE)
```

## Exploring the data

We now have a dataframe of `highly_conneted_coordinated_entities` that
repeatedly shared the same URLs within the coordination interval.

And now we’ll display the top 50 entities sorted by coord.shares in an
inline table:

``` r
# Load DT package for displaying inline tables
library(DT)

# Display inline table of top 50 Facebook groups identified by CooRNet, sorted by coord.shares
datatable(head(highly_connected_coordinated_entities_names, 50), options = list(order = list(list(3, 'desc'))))
```

<!--html_preserve-->

<div id="htmlwidget-582bdaaeaa841d2fc36e" class="datatables html-widget" style="width:100%;height:auto;">

</div>

<script type="application/json" data-for="htmlwidget-582bdaaeaa841d2fc36e">{"x":{"filter":"none","data":[["53","41","198","176","351","57","9","87","590","10","586","55","322","518","40","48","254","705","1016","1473","23","271","858","214","161","363","365","953","954","1658","70","95","428","683","1195","65","242","257","270","324","330","366","697","709","744","994","1165","1502","135","184"],["X22 Report [Geopolitical]","Collective Action Against Bill Gates. We Wont Be Vaccinated!!","99% unite Main Group \"it's us or them\"","OFFICIAL Q / QANON","Qanon deutsch blumenberger","FOX NEWS with Tucker Carlson","WWG1 WGA...Q","&gt;&gt; Fall of the Cabal &lt;&lt;","Drain The Swamp","Q The Greatest Story Ever Told","Neues Forum 89 Leipzig","X22 Report [Financial]","Real Qanon Follow The White Rabbit","QAnon -Posts by Q","QANON PIZZAGATE FULL DISCLOSURE GROUP","REOPEN NJ","Chemtrails Global Skywatch","The Official Rush Limbaugh Facebook Group","Q The Great Awakening","„Qanon Europa“- #wwg1wga","The Official President Donald J. Trump Fan Club","Monmouth County News","The Great Awakening","FIRST OFFICIAL CANDACE OWENS FAN CLUB","Morris San","Wisconsin Conservatives","Restore Wisconsin #Restore Wisconsin #Reopen Wisconsin","ReOpen California","Nevadans Against Extended Quarantine","Lowell, Indiana","Trump Strong","Vaccini Puliti. Rimozione dal commercio dei prodotti vaccinali contaminati","Republicans of California (Statewide)","Banned.Video Group","Donald Trump For President 2020!!!!!!","Trump / Pence AGAIN in 2020 (c)","CoronaVirus Conspiracy Theories","Galactic Federation of Light","Full Disclosure Collaborative","#ReOpenOC","CUOMO NEEDS TO GO!!!","CANDACE OWENS FAN CLUB","The 17th letter of the alphabet","Michiganders for Constitutional rights.","Werner Altnickel - Globale Vergiftung durch Chemtrails &amp; HAARP","ReOpenMN","Trudeau Watch Group","coronadatencheck.com","Sostenitori di Byoblu (gruppo non ufficiale)","Exposing Every Evil"],[60106,48940,32487,126021,27488,26061,24746,31954,73597,22554,17285,21140,12592,32763,14320,17071,181187,49749,15762,16293,16848,46370,17843,25558,26922,11564,2578,11727,11006,17961,15956,19209,14639,10594,178199,11926,10369,75598,13881,9973,13383,17332,4077,3954,24279,2259,5872,8512,19968,15501],[41,36,29,27,22,21,20,16,15,14,14,13,13,13,12,12,12,12,11,11,10,10,10,9,8,8,8,8,8,8,7,7,7,7,7,6,6,6,6,6,6,6,6,6,6,6,6,6,5,5],[1842,1560,1876,1348,625,1736,1825,1682,1746,1059,860,1548,1280,1438,1028,1451,1323,1213,1319,741,1453,1035,1243,872,1385,1247,1455,1039,1313,673,1303,85,1205,954,1313,662,1277,1277,819,963,432,1273,1013,937,905,789,1313,735,509,909],[2303,1988,2325,1614,667,2142,2341,2026,2242,1236,970,1865,1507,1734,1162,1794,1576,1421,1541,783,1725,1163,1468,952,1628,1434,1755,1173,1532,788,1505,85,1390,1097,1532,724,1511,1511,901,1104,441,1448,1147,1048,1016,855,1596,774,528,991]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>account.name<\/th>\n      <th>avg.account.subscriberCount<\/th>\n      <th>coord.shares<\/th>\n      <th>degree<\/th>\n      <th>strength<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"order":[[3,"desc"]],"columnDefs":[{"className":"dt-right","targets":[2,3,4,5]},{"orderable":false,"targets":0}],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

<!--/html_preserve-->

But what is the threshold that defines a rapid link share for these
highly connected entities? To determine that, we ran the
`estimate_coord_interval` function in `CooRNet`.

``` r
cord_int<-estimate_coord_interval(ctshares, q=0.1, p=0.5)

cord_int
```

    ## [[1]]
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##       0       9      14    7399    4251  103950 
    ## 
    ## [[2]]
    ## [1] "14 secs"

This returned a coordination interval of 14 seconds. A link share
between two groups that occurred within 14 seconds is defined as
unusually rapid, relative to the rest of the dataset.

## The Top Shared URLs

We also wanted to a plot of the top URLs in the dataset that were being
rapidly shared among the groups. We first got the list of URLs using the
`get_top_coord_urls` function in \`CooRNet.

``` r
# Get top URLs
top_urls_all <- get_top_coord_urls(output, order_by = "shares", component = FALSE, top = 6)

#Drop unwanted columns
top_urls_all <- select(top_urls_all, expanded, shares, engagement)

# Display as inline table 
datatable(top_urls_all) %>%
  formatStyle(names(top_urls_all), lineHeight='1%')
```

<!--html_preserve-->

<div id="htmlwidget-b474f2818ef966aba749" class="datatables html-widget" style="width:100%;height:auto;">

</div>

<script type="application/json" data-for="htmlwidget-b474f2818ef966aba749">{"x":{"filter":"none","data":[["1","2","3","4","5","6"],["https://vimeo.com/414798216","https://youtu.be/fsi9csLNb-Y","https://youtu.be/nFPeN17PVU8","https://youtu.be/ICtsXNtf_GQ","https://plandemicmovie.com","https://vimeo.com/414289492"],[484,477,470,458,365,338],[8349,11027,8885,8771,null,6168]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>expanded<\/th>\n      <th>shares<\/th>\n      <th>engagement<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,3]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false,"rowCallback":"function(row, data) {\nvar value=data[1]; $(this.api().cell(row, 1).node()).css({'line-height':'1%'});\nvar value=data[2]; $(this.api().cell(row, 2).node()).css({'line-height':'1%'});\nvar value=data[3]; $(this.api().cell(row, 3).node()).css({'line-height':'1%'});\n}"}},"evals":["options.rowCallback"],"jsHooks":[]}</script>

<!--/html_preserve-->

All of the top URLs shared by the entities were links to the Plandemic
movie on YouTube, Vimeo, or PlandemicMovie.com.

## Visualizing the network in Gephi

One of our outputs – `highly_connected_g` – was a large igraph object
representing a network. The next step was to prepare this network for
analysis in Gephi. We obtained summary statistics for `degree`. In the
study of networks, degree is the number of connections a node has to
other nodes. For the purposes of our data, nodes were individual
Facebook groups, and connections were shares of URLs.

``` r
summary(V(highly_connected_g)$degree)
```

To make the graph less cluttered, we filtered it by deleting all
vertices with a degree less than 1000. This will leave us with only the
most connected Facebook groups.

``` r
library(igraph)

g <- delete.vertices(highly_connected_g, V(highly_connected_g)[degree < 1000])

#Export the graph as a graphml object

write.graph(g, file = "g.graphml", format = c("graphml"))
```

Our work was done in R for now, and we were ready to move to Gephi.

After exporting the graphml file from R, we imported it into Gephi. The
result was something that looked like this:

![Opening the graphml file in
Gephi.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%201%20.png)

We turned this network into a meaningful visualization through a series
of steps.

First, in the Data Laboratory, we copied the data from the account.name
column to Label.

![Label each node with
account.name.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%202.png)
Back in Overview, we started editing the visualization. We sized the
nodes by coordinated.share. Groups with a larger number of coordinated
shares were represented by larger nodes.

![Sizing nodes by
coordinated.share.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%203.png)
Next, we colored the nodes by degree, an option under the “Ranking” tab.

![Coloring nodes by
degree.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%204.png) Color
the edges by weight, under “Ranking.” Edges with a higher weight – more
shares from between the two groups – will be a darker shade.

![Coloring edges by
weight.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%205.png) After
running the Force Atlas 2 algorithm with Scaling = 200, we got this:

![After running Force Atlas
2.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%206.png) And then
immediately after, we ran the Noverlap algorithm:

![After running
Noverlap.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%207.png)

Toggling on node labels and running the Label Adjust algorithm will get
you a graph like this:

![After running Label
Adjust.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%208.png)

We are ready to move to rendering the graph in Preview. Here are our
settings:

![Preview
settings.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%209.png)

…And we can export the graph as a PNG. You can do further editing, such
as adding a title and annotations, in Inkscape or Adobe Illustrator.

![Exporting the
graph.](/Users/zkharazian/Desktop/Plandemic/Images/Gephi%2010.png)

## Constructing a Word Network

To better understand the topics of these conversations on CrowdTangle,
we next constructed what is known as a bigram word network of commonly
co-occurring pairs of words. (In the original article, we analyzed
Twitter data – in this guide, we chose to replicate the analysis with
data from CrowdTangle).

If you want to learn more about this approach to text mining, we suggest
this lesson on [Earth Data
Science](https://www.earthdatascience.org/courses/earth-analytics/get-data-using-apis/text-mining-twitter-data-intro-r/)
as well as this guide on
[tidytextmining.com](https://www.tidytextmining.com/ngrams.html), which
both inspired this analysis.

We are going to read in a list of posts containing URLs obtained from
CrowdTangle’s Historical Search. All of the URLs lead either to a copy
of the video hosted off of Facebook, such as on YouTube or BitChute, or
another source supporting the Plandemic conspiracy. In other words,
posts debunking the conspiracy have been excluded.

``` r
#Read in CSV
posts_plandemic_FB <- read.csv("/Users/zkharazian/Downloads/2020-05-31-17-22-10-EDT-Historical-Report-plandemic-urls-2020-05-05--2020-06-01.csv")

head(posts_plandemic_FB$Message)

#Install additional required packages 
library(tidytext)
library(ggplot2)
library(widyr)
library(igraph)
library(ggraph)
```

``` r
#Strip URLS out. We want to do a text analysis of the most common words in posts, so the URLs won't be helpful.

posts_plandemic_FB$stripped_text <- gsub("http.*","", posts_plandemic_FB$Message)
posts_plandemic_FB$stripped_text <- gsub("https.*","", posts_plandemic_FB$stripped_text)

head(posts_plandemic_FB$stripped_text)

# remove punctuation, convert to lowercase, add id for each post

posts_plandemic_paired_words <- posts_plandemic_FB %>%
  dplyr::select(stripped_text) %>%
  unnest_tokens(paired_words, stripped_text, token = "ngrams", n = 2)

posts_plandemic_paired_words %>%
  count(paired_words, sort = TRUE)

posts_plandemic_separated_words <- posts_plandemic_paired_words %>%
  separate(paired_words, c("word1", "word2"), sep = " ")

posts_plandemic_filtered <- posts_plandemic_separated_words %>%
  filter(!word1 %in% stop_words$word) %>%
  filter(!word2 %in% stop_words$word)

# new bigram counts:
plandemic_words_counts <- posts_plandemic_filtered %>%
  count(word1, word2, sort = TRUE)

head(plandemic_words_counts)
```

Next, we are going to plot the word network, filtering out all word
pairs occurring fewer than 26 times in the text corpus.

``` r
# plot plandemic word network

plandemic_words_counts %>%
  filter(n > 26) %>% 
  graph_from_data_frame() %>%
  ggraph(layout = "fr") +
  # geom_edge_link(aes(edge_alpha = n, edge_width = n)) +
  geom_edge_link() +
  geom_node_point(color = "dodgerblue2", size = 4) +
  geom_node_text(aes(label = name), vjust = 1.8, size = 3) +
  labs(title = "Word Network: Co-occurring words in public posts spreading the Plandemic conspiracy",
       subtitle = "Text mining CrowdTangle data (n>26)",
       x = "", y = "") +
  theme_void()
```

![](Plandemic_How_to_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

The above plot shows some of the most common word paths appearing
alongside each other in posts sharing the Plandemic conspiracy to public
Facebook groups. From here, we can extract several common topics of
discussion.

On Facebook, the discussion appeared to focus on conspiracies involving
“powerful elites,” with references to Bill Gates, the “political
elite,” and “billionaire patent owners.” This is in contrast to Twitter,
where, in our original analysis, we had found a focus on the anticipated
censorship of the video.

![Earlier Twitter Word Network for
comparison.](/Users/zkharazian/Desktop/Plandemic/Images/Twitter%20Word%20Network.png)

## Conclusion

This walkthrough replicated the DFRLab’s analysis of Plandemic, with a
focus on explaining and extending the portion using CrowdTangle. Because
of platform design and varying levels of data availability among
platforms, social media network analysis has more often relied on
Twitter data, and the network dynamics of other platforms are
understudied.

CrowdTangle and innovate third-party tools such as `CooRNet`, however,
offer several creative avenues for conducting network analyses using
Facebook data; here, the DFRLab demonstrated just one possible approach.
