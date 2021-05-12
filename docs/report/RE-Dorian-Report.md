---
layout: page
title: RE- Spatial-temporal and content analysis of Twitter Data
---


**Replication of**
# Spatial, temporal and content analysis of Twitter data

Original study *by* Wang, Z., X. Ye, and M. H. Tsou. 2016. Spatial, temporal, and content analysis of Twitter for wildfire hazards. *Natural Hazards* 83 (1):523–540. DOI:[10.1007/s11069-016-2329-6](https://doi.org/10.1007/s11069-016-2329-6).
and
First replication study by Holler, J. 2021 (in preparation). Hurricane Dorian vs Sharpie Pen: an empirical test of social amplification of risk on social media.

Replication Author:
Arielle Landau

Replication Materials Available at: [RE-dorian](https://github.com/alandaux/RE-Dorian)

Created: `4/4/2021`
Revised: `4/1/2021`

## Abstract

Why study the spatial distribution of Twitter data?

Wang et al (2016) analyzed Twitter data for wildfires in California, finding that social media data can provide useful information on natural disasters, especially for updates on damage and response times. They additionally found the news media tend to be opinion leaders, as sources of information during a natural disaster.

Holler (2021) is studying Twitter data for Hurricane Dorian on the Atlantic coast, finding that in spite of tending news and social media content regarding a false narrative of risk, original Tweets still clustered significantly along the real hurricane track, and only along the hurricane track.

Reproducing and replicating geospatial research that relies upon social media data continues to be relevant because of the constant fire hose of information available, as well as the addition of ethical concerns and privacy issues surrounding big data.

In this replication study, I will look at tweets surrounding Bird Day on May 4th, 2021 to compare twitter as an open source resource for birding information, compared to ebird, another site of volunteered geographic information. May 4th was chosen as the search date for the twitter API in the hopes that a designated day for birds would generate enough tweets for a robust geospatial analysis.



## Original Study Information

Wang et al. (2016) studied wildfire related tweets through a spatial and temporal analysis, as well as a network analysis. Three main analytical techniques were used: kernal density estimation, text mining and social network analysis.Kernal density estimation was used to create a map of wildfire related tweets, taking coordinates of tweets and exporting a raster with a value representing the number of tweets for an area. Further, a dual kernel density estimation was used to visualize tweet locations for two specific fires. Text mining was used to identify important terms used in wildfire related tweets (using the “tm” package in R 3.12), and social network analysis was used to understand the behavior of users communicating about wildfires through retweets (using a k-mean clustering method through the R package “igraph”). Results showing the number of tweets over time were displayed using bar charts, while the spatial distribution of wildfire tweets (displayed using different sized centroids), and the kernal density analyses were visualized through maps (displayed using a heat map). Term frequency was visualized using a bar chart as well. Term cluster results were displayed in a table and the distribution of the retweet network was visualized in line graphs. The retweet network was visualized in a node diagram where nodes were drawn between users when one retweeted another.

Holler (2021) loosely replicated the methods of Wang et al (2016) for the case of Hurricane Dorian's landfall on the U.S. mainland during the 2019 Atlantic Hurricane season. Data was based on Twitter Search API queries for the keywords hurricane, Dorian or sharpiegate.

Holler modified Wang et al's methods by not searching for retweets for network analysis, focusing instead on original Tweet content with keywords hurricane, Dorian, or sharpiegate (a trending hashtag referring to the storm). Holler modified the methodology for normalizing tweet data by creating a normalized Tweet difference index and extended teh methodology to test for spatial clustering with the local Getis-Ord statistic. The study tested a hypothesis that false narratives of hurricane risk promulgated at the highest levels of the United States government would significantly distort the geographic distribution of Twitter activity related to the hurricane and its impacts, finding that original Twitter data still clustered only in the affected areas of the Atlantic coast in spite of false narratives about risk of a westward track through Alabama.

Wang et al (2016) conducted their study using the `tm` and `igraph` packages in `R 3.1.2`.
The replication study by Holler (2021) used R, including the rtweet, rehydratoR, igraph, sf, and spdep packages for analysis.

## Materials and Procedure

In order to find bird related tweets in and around Bird Day on May 4th, the following query was used in the Twitter API:
    "birdday OR birding OR birds OR (bird AND spring) OR (bird AND migration)"
Using the word "bird" on its own was eliminated from the query in order to avoid the many tweets where men refer to their significant others as "my bird" (the slang meaning of bird). After cleaning the data to tweets that included geographic information, 1347 tweets remained. 200,000 tweets were also fetched using the twitter API in order to normalize the data when calculating the normalized difference between tweets index. The normalized difference between tweets index = (tweets about birds – baseline twitter activity) / (tweets about birds + baseline twitter activity).

The bird related tweets are available [here](data/derived/public/birding.RDS)
The May baseline tweets are available [here](data/derived/public/mayTweets.RDS)

The temporal analysis was conducted by first grouping the twitter data by hour and then graphing the results with ts_plot.
```
birdsByHour <- ts_data(birding, by="hours")
ts_plot(birding, by="hours")
```

The network analysis was conducted by using a network graph and then plotted using igraph.
```
birdingNetwork <- network_graph(birding, c("quote"))
plot.igraph(birdingNetwork)
```

The contextual analysis was conducted by first cleaning up the twitter content to exclude stop words and URLs. Stop words included the words used in the search query. The frequencies of words were then graphed, in addition to word pair frequencies and a word cloud with word associations.

The spatial clustering analysis was conducted by first joining counties to each tweet, and then by grouping tweets by county to count the numebr of tweets in each county. The counts of tweets in each county was then joined back to the original county data. The same steps were done for the May baseline tweets. Threshold distance was then calculated on the counties to create a weight matrix of neighbor objects. Then a Getis Ord statistic was used to calculate hot and cold spots.

Specific code for my analysis can be found [here](procedure/code/Birding Analysis Code)

## Replication Results

Temporal Analysis of Bird Related Tweets
![Temporal Analysis](figures/birdsbyhour)
There is a general pattern of the number of bird related tweets rising in the morning. Notably, there is a spike of bird related tweets on April 30th.

Count of Unique Words found in Bird Related Tweets
![Unique words](figures/birdUniqueWords)
The most common word found in bird related tweets was morning, corresponding to the spike of bird related tweets seen in the morning hours in the temporal analysis.

Word Network of Bird Tweets around Bird Day
![Word Network of Birds](figures/wordNetworkOfBirdTweets)
The word network of tweets from the contextual analysis begins to show where there are strong birding communities, such as those around central park.

Tweet Locations Around Bird Day
![Bird Twitter Activity](figures/tweetLocationsAroundBirdDay)
Locations of bird related tweets are pretty sporadic, but there seems to be higher numbers of tweets along the East Coast, starting around Virginia to Vermont.

Clusters of Twitter Activity Around Bird Day
![Hot Spot Analysis](figures/clustersofTwitterActivityAroundBirdDay)
Cluster of high activity of bird related tweets around bird day can be found on the East Coast, on the southern tip of florida, on the Louisiana coast, in Alabama and northern Indiana/ southern Michigan.

## Unplanned Deviations from the Protocol

In order to start with enough tweets for a robust analysis, the query to search for bird related tweets was changed until enough tweets were procured. Originally the query only searched for tweets with (bird AND spring) OR (bird AND migration). Then birding, birdday and birds was added to the query, making the final query:
"birdday OR birding OR birds OR (bird AND spring) OR (bird AND migration)".

Additionally, there were packages in the R. script that were initially missing that were required to count and plot different aspects of tweets. Emma Brown figured out that we needed to install the tidyverse R. package.

For the the spatial clustering analysis, the original plan was to use database queries in QGIS, but then code was provided by Joe Holler to conduct the analysis in R. along with the rest of the analyses.

## Discussion

For both Wang et al. (2016) and Holler (2021), the working hypothesis was that tweets are not random, but are actually clustered. For my analysis, there was an exploratory hypothesis as well as a working hypothesis. The exploratory hypothesis was to see if twitter could be a used for volunteered geographic information about bird locations in the same way that ebird is. The working hypothesis was the same as Wang and Holler that tweets are not random but are clustered. My analysis found clusters of tweets, where there were areas of low and high activity similar to that of Wang and Holler. However, unlike Wang and Holler, which were centered around the analysis of one geospatial event, wildfires in Califronia, and Hurricane Dorian, respectively, my analysis on bird related tweets around Bird Day did not center around one geospatial event. With only 1347 bird related tweets that included geographic information, twitter does not match up to ebird as a source of volunteered geographic information about bird locations.

On April 30th there was a spike in bird related tweets, likely because April 30th was world migratory bird day. There was also a pattern of bird related tweets increasing in the morning, which corresponded to morning having the greatest word frequency. The contextual analysis also revealed prominent bird communities, such as central park. Similar to Wang et al. where opinion leaders were identified as mostly media outlets, my analysis also found opinion leaders in more official twitter accounts such as sibirdsclub and wwfscotland. Spatial clustering was much less concentrated than in Wang's and Holler's results as my search query did not focus on one geospatial event. However, the number of clusters around large bodies of water, mostly the ocean, shows a pattern of bird watchers concentrated on spaces where one is likely to see large numbers of migratory birds.


## Conclusion

Like Wang and Holler, my analysis found clustering of tweets, indicating that tweets are not random. Moreover, my analysis found a spike in twitter activity on World Migratory Bird Day, and saw more hot spots of twitter activity along coastlines, where one is more likely to see migratory birds. My analysis also found increase in bird related tweet activity in the mornings, and was also able to identify birding communities, such as those around central park. Future analysis could be done on other outdoor related hobbies to understand the dynamics of volunteered geographic information for hobbies such as fishing, hunting or boating.

## References

Holler, J. 2021 (in preparation). Hurricane Dorian vs Sharpie Pen: an empirical test of social amplification of risk on social media.

Wang, Z., X. Ye, and M. H. Tsou. 2016. Spatial, temporal, and content analysis of Twitter for wildfire hazards. Natural Hazards 83 (1):523–540. DOI:10.1007/s11069-016-2329-6

####  Report Template References & License

This template was developed by Peter Kedron and Joseph Holler with funding support from HEGS-2049837. This template is an adaptation of the ReScience Article Template Developed by N.P Rougier, released under a GPL version 3 license and available here: https://github.com/ReScience/template. Copyright © Nicolas Rougier and coauthors. It also draws inspiration from the pre-registration protocol of the Open Science Framework and the replication studies of Camerer et al. (2016, 2018). See https://osf.io/pfdyw/ and https://osf.io/bzm54/

Camerer, C. F., A. Dreber, E. Forsell, T.-H. Ho, J. Huber, M. Johannesson, M. Kirchler, J. Almenberg, A. Altmejd, T. Chan, E. Heikensten, F. Holzmeister, T. Imai, S. Isaksson, G. Nave, T. Pfeiffer, M. Razen, and H. Wu. 2016. Evaluating replicability of laboratory experiments in economics. Science 351 (6280):1433–1436. https://www.sciencemag.org/lookup/doi/10.1126/science.aaf0918.

Camerer, C. F., A. Dreber, F. Holzmeister, T.-H. Ho, J. Huber, M. Johannesson, M. Kirchler, G. Nave, B. A. Nosek, T. Pfeiffer, A. Altmejd, N. Buttrick, T. Chan, Y. Chen, E. Forsell, A. Gampa, E. Heikensten, L. Hummer, T. Imai, S. Isaksson, D. Manfredi, J. Rose, E.-J. Wagenmakers, and H. Wu. 2018. Evaluating the replicability of social science experiments in Nature and Science between 2010 and 2015. Nature Human Behaviour 2 (9):637–644. http://www.nature.com/articles/s41562-018-0399-z.
