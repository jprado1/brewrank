---
layout: default
---

We all love to relax and chat with friends. Who doesn't love a cold brew ? Drinking beer with people is an important social ritual that is often linked to many memories (or the absence of it), especially of our younger days. EPFL is a highly international institution with people from all around the world. They leave their home countries behind to join EPFL but what if we could bring them back a bit of it ? We decided to match beers from SAT, our beloved bar, to the countries where they would be the most appreciated, to recommend them to people missing home and hope it might cure a bit of this homesickness !

## A quick sip through our data

#TODO : 


| Dataset  | 🍺   |  🍶  | 👥| 💸  |
|:--------|:-------:|--------:|
| RateBeer  | XX beers from YY countries  |  XX beer styles  | ZZ beer lovers from SS countries | No data  |
| BeerAdvocate  | XX beers from YY countries  | XX beer styles  | ZZ beer lovers from SS countries  | No data   |
| Satellite Bar @EPFL)  | 66 beers from 8 countries  | 21 beer styles  | No data  | Beer prices from 3.- to 16.- CHF  |


- [Qu'est ce que vous pensez?] Des phrases/bullet point rapides (~100 mots) sur les types de correction/hypothèses qu'on a fait/nous principaux utils de travail ?


## An iconic duo : beer opinions and bias

Joli text sur debiasing ici. Possiblement avec un organigramme explicatif de la pipeline de correction
    
## National treasures : best brew by country and the happiness they bring

Texte de la partie de Gab ici
TODO:

- adapter le plot pour qu'on puisse choisir le dataset avec un bouton
- changer la couleur de fond du plot si on n'utilise un background blanc pour le site 

@Joao : rajouter graphs de bières plus aimées par pays et de "hidden gems" (bières très bien côtés mais vues par un nombre restreint de personnes (5 < n < 10 peut être? à discuter)


{% include map_favourite_beer_RB_country.html %}
{% include map_favourite_beer_BA_country.html %}

## A trip to Satellite : learning beer quality from the WWW wisdom


In order to propose the best possible assortment of beers sold on campus given the opinion of our internet oracle, we devised the following approach:
- We scrapped and cleaned the full beer SAT menu from their webpage. (You can check their menu here)
- We adapted an implementation of Vector Space Retrieval (VSR) retrieved from (CS-423) in order to match SAT beers with beers reviewed in our datasets. For each dataset, we run VSR to find the top 5 matches for a given SAT beer. Matching is done based on the cosine similarity of ‘SAT beer queries’ and ‘Dataset documents’ . In this context, a ‘query’ and ‘document’ are string objects constructed by concatenating and stemming beer name, brewery and ABV.
- After a first analysis of our matches, we developed an heuristic in order to reproducibly recover the ratings of all SAT beers.
    - Matches with more than 0.8 cosine similarity and in which ABV matches perfectly are considered perfect matches. Our reasoning is that, since reporting correct ABVs is a legal obligation in many countries
    - For beers without a perfect match, we manually check for the best match in the group of top 5 retrievals. 
    - For beers without match, we train a Random Forest model in order to roughly estimate what would be the beer rating. In this case, features for learning are constructed from country of origin of the brewery, ABV and beer style.

You can see our results in the following graph. We also proposed a ranking where ratings are normalised by price and volume for our fellow economically constrained colleagues!


![](/images/SATpipelineV1.png){:class="img-responsive"}

@Joao : Rajouter texte de comment les données ont été traitées et des outils utilisés
{% include sat_rank_separated.html %}
{% include RB_wordclouds.html %}

### Missing home? Then read carefully

Given our recently acquired knowledge about the general rating of SAT beers, we try to answer the following question : 
Can we generate recommendations of SAT beers that are similar enough to what users of a specific country appreciate ? In the negative case, can we propose additions to the SAT menu that would please members the international communities of EPFL?

We approach this challenge by first querying our datasets for the preferred beer of users of each country. We define ‘preferred beer according to dataset X’ as the item of 'X' that received the bigger average rating of users of a given country, conditioned on the item having at least 10 ratings. Countries whose citizens have not, in total, reviewed beers more than 10 times are unfortunately excluded from our analysis. 

In order to design a good similarity metric, we harness the capabilities of one of the most recent open foundation model as of December 2022. For each textual review, we requested one embedding from OpenAi’s ADA-002 API. This approach has the benefits of being overall robust to multilingual datasets (such as ours) and  would allow, in a future work, to benchmark our results against some future work that share the same latent space. The technical details of this approach can be found in our accompanying notebook.

We visualise the implicit clustering generated by these embeddings with t-SNE plots : 

{% include RB_tsne.html %}
{% include BA_tsne.html %}


@Joao : Rajouter description courte de comment on met en rapport les bières aimées en chaque pays et les bières vendues à sat 

## To wrap up

## You are still there ? Please have a beer GIF:

![](/images/test_gif_beer.gif){:class="img-responsive"}
