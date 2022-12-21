---
layout: default
usemathjax: true
---

We all love to relax and chat with friends. Who doesn't love a cold brew ? Drinking beer with people is an important social ritual that is often linked to many memories (or the absence of it), especially of our younger days. EPFL is a highly international institution with people from all around the world. They leave their home countries behind to join EPFL but what if we could bring them back a bit of it ? We decided to match beers from SAT, our beloved bar, to the countries where they would be the most appreciated, to recommend them to people missing home and hope it might cure a bit of this homesickness !

## A quick sip through our data



| Dataset  | Beers 🍺   | Brew styles 🍶  | Beer lovers 👥| Beer cost 💸  |
|:--------|:-------:|--------:|
| RateBeer  | 396690 beers from 265 locations*  |  93 beer styles  | 70120 beer lovers from 222 locations* | No data  |
| BeerAdvocate  | 247982 beers from 264 locations*  | 104 beer styles  | 153704 beer lovers from 194 locations*  | No data   |
| Satellite Bar   | 66 beers from 8 countries  | 21 beer styles  | No data  | Beer prices from 3.- to 16.- CHF  |

*Location can be either countries or regions of countries with many active users (e.g. Individual states of the United States or regions of the United Kingdom)

Our assumptions and decisions and when processing these datasets were:

* Users without a defined country in the dataset were considered to have "Unknown" location. Their data is not considered in the beer preference world map we show, but they are considered in the SAT t-SNE clustering plots.
* Beers without rating and breweries without beers were not considered.


## An iconic duo : beer opinions and bias



One of the main challenges when analysing data from rating and review systems comes from the fact that humans are prone to bias. One’s personal bias is not only hard to quantify but may be correlated with other personal features (place of birth, age and personal experiences, to give a few examples).

Inspired by [this paper](/https://krisjensen.github.io/files/bias_blog.pdf/) on the modelling and correction of bias of NeurIPS papers, we aim to decrease the effect of systematic reviewer bias by applying a ‘mean-field’ correction to our datasets’ ratings. 


![](/images/equation.png){:class="img-responsive"}

where $r_{ij}$ is the rating given by user $$i$$ to beer $$j$$, $$\alpha_i$$ is an attenuation factor taking into consideration the number of reviews user $i$ compared to the whole dataset, $$b_i$$ is the systematic bias of user $$i$$, $$n_i$$ is the total number of reviews of user $$i$$ and $$N$$ is the total number of reviews of the dataset

## National treasures : best brew by country and the happiness they bring

Texte de la partie de Gab ici


{% include map_favourite_beer_RB_country.html %}
{% include map_favourite_beer_BA_country.html %}

## A trip to Satellite : learning beer quality from the WWW wisdom


In order to propose the best possible assortment of beers sold on campus given the opinion of our internet oracle, we devised the approach shown in this pipeline:

![](/images/SATpipelineV1.png){:class="img-responsive"}


- We scrapped and cleaned the full beer [SAT menu](/https://satellite.bar/bar/), preparing it for analysis.
- We adapted an implementation of Vector Space Retrieval (VSR) retrieved from (CS-423) in order to match SAT beers with beers reviewed in our datasets. For each dataset, we run VSR to find the top 5 matches for a given SAT beer. Matching is done based on the cosine similarity of ‘SAT beer queries’ and ‘Dataset documents’ . In this context, a ‘query’ and ‘document’ are string objects constructed by concatenating and stemming beer name, brewery and ABV.
- After a first analysis of our matches, we developed an heuristic in order to reproducibly recover the ratings of all SAT beers.
    - Matches with more than 0.8 cosine similarity and in which ABV matches perfectly are considered perfect matches. Our reasoning is that, since reporting correct ABVs is a legal obligation in many countries
    - For beers without a perfect match, we manually check for the best match in the group of top 5 retrievals. 
    - For beers without match, we train a Random Forest model in order to roughly estimate what would be the beer rating. In this case, features for learning are constructed from country of origin of the brewery, ABV and beer style.

You can see our results in the following graph. We also proposed a ranking where ratings are normalised by price and volume for our fellow economically constrained colleagues!


{% include sat_rank_separated.html %}

### Missing your home country during the harsh winter of 2022? This is for you:

Given our recently acquired knowledge about the general rating of SAT beers, we try to answer the following question : 
Can we generate recommendations of SAT beers that are similar enough to what users of a specific country appreciate ? In the negative case, can we propose additions to the SAT menu that would please members the international communities of EPFL?

We approach this challenge by first querying our datasets for the preferred beer of users of each country. We define ‘preferred beer according to dataset X’ as the item of 'X' that received the bigger average rating of users of a given country, conditioned on the item having at least 10 ratings. Countries whose citizens have not, in total, reviewed beers more than 10 times are unfortunately excluded from our analysis. 

In order to design a good similarity metric, we harness the capabilities of one of the most recent open foundation model as of December 2022. For each textual review, we requested one embedding from OpenAi’s ADA-002 API. This approach has the benefits of being overall robust to multilingual datasets (such as ours) and  would allow, in a future work, to benchmark our results against some future work that share the same latent space. The technical details of this approach can be found in our accompanying notebook.

We visualise the implicit clustering generated by these embeddings with t-SNE plots : 


| RateBeer styles  | BeerAdvocate styles|
|:--------|--------:|
| {% include RB_tsne.html %}| {% include BA_tsne.html %}  | 

Nevertheless, our analysis is not without weaknesses. One of the biggest challenges in recommendation systems is  the cold start problem : How to recommend beers to members of the EPFL community if we do not have access to their own reviews ? Is it really reasonable to approximate one’s beer likings with those of their compatriots?

Unfortunately, we believe that the answer for this last question is, in general, no. For nationals from countries excluded in our analysis, and for those that do not identify with the tastes of their fellow country(wo)men, we propose a deep-dive on the most common terms associated with each beer style available at each dataset. 

{% include RB_wordclouds.html %}
{% include BA_wordclouds.html %}


## To wrap up



## You are still there ? Please have a beer GIF:

![](/images/test_gif_beer.gif){:class="img-responsive"}
