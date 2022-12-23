---
layout: default
usemathjax: true
classes: wide
---

We all love to relax and chat with friends. Who doesn't love a cold brew ? Drinking beer with people is an important social ritual that is often linked to many memories (or the absence of them), especially of our younger days. EPFL is a highly international institution with people from all around the world. They leave their home countries behind to join EPFL but what if we could bring them back a bit of it ? We decided to match beers from SAT, our beloved bar, to the countries where they would be the most appreciated. Our objective is to recommend them to people missing home and hope it might cure a bit of this homesickness ! It is also a tool to ask SAT to buy new beers, since it would be sad to leave people without a beer similar to the one from their country, don't you think ?

## A quick sip through our data



| Dataset  | Beers 🍺   | Brew styles 🍶  | Beer lovers 👥| Beer cost 💸  |
|:--------|:-------:|--------:|
| RateBeer  | 396690 beers from 265 locations*  |  93 beer styles  | 70120 beer lovers from 222 locations* | No data  |
| BeerAdvocate  | 247982 beers from 264 locations*  | 104 beer styles  | 153704 beer lovers from 194 locations*  | No data   |
| Satellite Bar   | 66 beers from 8 countries  | 21 beer styles  | No data  | Beer prices from 3.- to 16.- CHF  |

*Location can be either countries or regions of countries with many active users (e.g. Individual states of the United States or regions of the United Kingdom)

Our assumptions and decisions when processing these datasets were:

* Users without a defined country in the dataset were considered to have "Unknown" location. Their data is not considered in the beer preference world map we show, but they are considered in the SAT t-SNE clustering plots.
* Beers without rating and breweries without beers were not considered.
* After preliminary observations from an exploratory step of our analysis (available in the accompanying Jupyter Notebook, we considered that BeerAdvocate and RateBeer had considerably different communities. Despite having a dataset of matching users and beers available for our study, we performed our analysis in each dataset separatedly.  


## An iconic duo : beer opinions and biases



One of the main challenges when analysing data from rating and review systems comes from the fact that humans are prone to bias. One’s personal bias is not only hard to quantify but may be correlated with other personal features (place of birth, age and personal experiences, to give a few examples).

Inspired by [this paper](https://krisjensen.github.io/files/bias_blog.pdf) on the modelling and correction of bias of NeurIPS papers, we aim to decrease the effect of systematic reviewer bias by applying a ‘mean-field’ correction to our datasets’ ratings. The pipeline to correct the beer ratings is described in the flowchart below and its accompanying text.

![](/images/Correction_flowchart.png)

We computed a bias for each user, which was attenuated by a coefficient comprised between 0 and 1 and inversely proportional to the number of ratings a user has provided the platform. By doing so, we wanted to attenuate the bias of one-time reviewers, and give more weight to the bias of hardened reviewers as this bias is more trustworthy. We then removed the users' bias from all the corresponding ratings, and recomputed average ratings for all beers from the debiased ratings of the users. Ratings were kept between 0 and 5. By doing so, we aimed at getting fairer and more representaive ratings of the quality of the beers. 

Applying this correction allowed us to witness an evolution in the distribution of ratings: 

![](/images/bias_graphs.png)

We can see a clear evolution in the distribution of ratings from the RateBeer website after the correction. The distribution shifts to the right: our correction led to a higher proportion of higher ratings, suggesting that users were grading harshly before correction. This effect is not observable in the case of the BeerAdvocate website. This could be because there is an outlier user having reviewed a very high number of beers compared to the other users. Because of this, the attenuation coefficient would become very small for most users, erasing their biases.



## National treasures : best brew by country and the happiness they bring

After some digging we found the beer most voted by each countries and assumed it to be the beer most drinked. It would seem that swiss enjoy BFM La Torpille or Feldschlossen Original Lager ! Users from RateBeer might be fancier than their BeerAdvocate counterpart.

As we can see the datasets are mainly representaive of the USA since each state is treated as a country and to a lesser degree Europe is also more represented than the rest of the world. It seems that RateBeer and BeerAdvocate aren't as popular in the rest of the world !

We also counted the mean positive and negative words used by each countries in their reviews to see if some countries were more prone to praise. It would seem that south Europe is harsher than north Europe, which is interesting. 

In the world-map we decided to use the state of California's data since it's the most populated state. We also did a zoom on the USA to show the whole dataset.

{% include map_favourite_beer_RB_country.html %}
{% include map_favourite_beer_RB_usa.html %}
{% include map_favourite_beer_BA_country.html %}
{% include map_favourite_beer_BA_usa.html %}


    
## A trip to Satellite : learning beer quality from the WWW wisdom


In order to propose the best possible assortment of beers sold on campus given the opinion of our internet oracle, we devised the approach shown in this pipeline:

![](/images/SATpipelineV1.png){:class="img-responsive"}


- We scrapped and cleaned the full beer [SAT menu](https://satellite.bar/bar/), preparing it for analysis.
- We adapted an implementation of Vector Space Retrieval (VSR) retrieved from (CS-423) in order to match SAT beers with beers reviewed in our datasets. For each dataset, we ran VSR to find the top 5 matches for a given SAT beer. Matching was done based on the cosine similarity of ‘SAT beer queries’ and ‘Dataset documents’ . In this context, a ‘query’ and ‘document’ are string objects constructed by concatenating and stemming beer name, brewery and ABV.
- After a first analysis of our matches, we developed an heuristic in order to reproducibly recover the ratings of all SAT beers.
    - Matches with more than 0.8 cosine similarity and in which ABV matches perfectly were considered perfect matches. 
    - For beers without a perfect match, we manually checked for the best match in the group of top 5 retrievals. 
    - For beers without match, we trained a Random Forest model in order to roughly estimate what would be the beer rating. In this case, features for learning were constructed from country of origin of the brewery, ABV and beer style.

One can see our results in the following graph. We also propose a ranking where ratings are normalised by price and volume for our fellow economically constrained colleagues!

{% include sat_rank_separated.html %}
    
We observed that Rochefort 10 and Rochefort 8 were consistently in the top 3 SAT beers, independently of the dataset used to rank beers sold on Satellite. Nevertheless, when we naively looked at the rank based on "rating per CHF and per serving", the cheaper beer won by far. A regression analysis showed that price, volume and the interaction between these two parameters account for 0.949 (RateBeer) and 0.948 (Beer Advocate) of the explained variance, confirming our intuition.

We can relate this observation to the fact that the overall rating of the 50 out of 66 best SAT beers is in a quite close window, between 3.3 and 4.1 for both datasets, after our correction for systematic bias. This would mean that, overall, almost all beers sold at SAT are quite good, and we should probably choose them based on other properties, like their style!


### Missing your home country during the harsh winter of 2022? This is for you:

Given our observations about the general quality of SAT beers, we tried to answer the following question : 
Can we generate recommendations of beers and styles sold on SAT that are similar enough to what users of a specific country appreciate ? In the negative case, can we propose additions to the SAT menu that would please members the international communities of EPFL?

We approached this challenge by first querying our datasets for the preferred beer of users of each country. We defined ‘preferred beer according to dataset X’ as the item of 'X' that received the higher average rating of users of a given country, conditioned on the item having at least 10 ratings. Countries whose citizens have not, in total, reviewed beers more than 10 times were unfortunately excluded from our analysis. 

In order to design a good similarity metric, we harnessed the capabilities of one of the most recent open foundation models as of December 2022. For each textual review, we requested one embedding from OpenAi’s ADA-002 API. This approach has the benefits of being overall robust to multilingual datasets (such as ours) and may allow, in a future work, to benchmark our results against some future work that shares the same latent space. The technical details of this approach can be found in our accompanying notebook.

We visualised the implicit clustering generated by these embeddings with t-SNE plots : 
{% include RB_tsne.html %}
{% include BA_tsne.html %}

* A cautionary notice : we suggest avoiding over-interpretating clusters of t-SNE plots. Not only t-SNE projections do not preserve distance metrics between embeddings, but they are also highly dependent on a 'perplexity' parameter that must be crossvalidated for interpretable results. Moreover, our embedding strategy depends heavily on OpenAi's embeddings API model, ADA-02. To our knowledge, embeddings provided by ADA-02 are not deterministic and can change between API requests. The results stated below were results found to be consistent across several iterations of our pipeline.
* When considering only the clustering of beers favoured by each country (represented by the respective country flag in the plot above), we observed quite different clusters when comparing embeddings generated from RateBeer or BeerAdvocate datasets. This can be seen as a validation of our preliminary hypothesis that both datasets are significantly different. 
* In the RateBeer t-SNE plot, we observed a cluster with most northern countries, Switzerland and Germany. Interestingly, all countries in this cluster prefer Imperial Stouts and are not directly close to any beer sold at SAT with reviews in our dataset. This could indicate that a new Imperial Stout may represent a nice addition to SAT's menu. We propose the beer representing Denmark, Sweden and Finland, [Närke Stormaktsporter](https://www.ratebeer.com/Ratings/Beer/Beer-Ratings.asp?BeerID=41771)
* Brazilian, portuguese, italian and german users of BeerAdvocate all seem to highly regard Rochefort Trappistes 10, the winner of our previously shown Satellite beer rank. They are already well served in our local bar!


Nevertheless, our analysis is not without weaknesses. One of the biggest challenges in recommendation systems is  the cold start problem : How to recommend beers to members of the EPFL community if we do not have access to their own reviews ? Is it really reasonable to approximate one’s beer likings with those of their compatriots?

Unfortunately, we believe that the answer for this last question is, in general, no. For nationals from countries excluded in our analysis, and for those that do not identify with the tastes of their fellow country(wo)men, we propose a deep-dive on the most common terms associated with each beer style available in each dataset. We hope this may help users to choose their next beer.



 {% include wordclouds.html %}

## To wrap up

We hope that our data story will help you choose your beer next time you visit SAT. In this work, we have not only proposed a new Imperial Stout for Sattelite’s menu, but we also analysed the general taste and positivity of beer lovers of different countries and took the challenge of dealing with systemic user bias. A beer loving data analyst may find interest in generalising our bias correction approach to other types of biases other than systematic user bias. Moreover, our approach of capitalising on OpenAI Embeddings API and ADA-002 model to produce embeddings vectors, may prove useful to future work interested in comparing review systems for product classes other than beers.


Thank you for reading, see you at SAT!
