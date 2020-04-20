# KG-COVID19Publications
Publications about COVID-19 are rapidly growing at this moment. In order to provide an efficient and easy way for researchers to be kept updated, I created a knowledge graph for COVID-19 publications. The knowledge graph will be updated every week. 

- Until 20-04-2020, 299,652 triples (based on 12116 publications) are in the current KG.

### Data resource 

- Publication dataset (xlsx file) is from [Dimensions](https://dimensions.figshare.com/articles/Dimensions_COVID-19_publications_datasets_and_clinical_trials/11961063). The data file contains all relevant publications, datasets and clinical trials from Dimensions that are related to COVID-19. Please download the dataset from its original source (Dimensions). This repo will not provide any data files.

- Dimensions is updated once every 24 hours, so the latest research can be viewed alongside existing information. 

- Content is identified in Dimensions with the search query: 

```PUBLICATION YEAR: 2020; FULL DATA SEARCH: "2019-nCoV" OR "COVID-19" OR “SARS-CoV-2” OR (("coronavirus" OR "corona virus") AND (Wuhan OR China))```

### Select key information of publications

- The dataset of COVID-19 publications has many missing values. The following picture shows the completeness of the dataset (updated at 14-04-2020) using ```check_missing``` function in ```publicationsData.ipynb```

  

- We only keep the key information for converting xlsx file to RDF using  ```publicationsData.ipynb```
  - *Title*
  - *DOI*
  - *Authors*
  - *Abstract*
  - *Langauge*
  - *Publication Type* (Book, article, preprint)
  - *Publication Date*
  - *Source title* (the source name of article)
  - *Publisher* 
  - *Paper URL in Dimensions*

### Extract keywords from titles ###

- To efficiently find relavant publications, keywords play an important role. Unfortunately, keywords were not collected in the Dimensions dataset. Therefore, I decided to extract keywords from titles. 
- Title is most direct and brief context telling the topics of the articles. Title is one of the most complete attributed (95%) in Dimensions dataset.
- I used [NLTK](https://www.nltk.org/) python tool to
  - Lemmatize titles (treats,treating,treated --> treat)
  - Tokenize words
  - POS tagging (Noun, Verb, Adj)
- So far, **92460** keywords have been extracted from **12116** publications titles.  

### Similarity of keywords

- To find the links/relations among publications in an efficient way, I made use of the similarity among keywords.  [Gensim](https://radimrehurek.com/gensim/index.html) was applied to calculate the similarity between keywords.
- For example:
  - *COVID-19* links to its similar keywords *sars-cov-2*, *2019-ncov*, *coronavirus*
  - *Surgery* links to *operation*, *surgeon*, *surgical*, *treatment*, *precaution*
  - Fatality links to *ascertainment*, *mortality*, *comorbidity*
  - Alzheimer links to *memory*, *noncommunicable*, *parkinson*, *proteostasis*

### Convert CSV to RDF

- **[Dublin Core Metadata Initiative Metadata Terms](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/)** is mainly used as the vocabulary.
- RML ([rmlmapper.jar](https://github.com/RMLio/rmlmapper-java/releases/download/v4.3.1/rmlmapper.jar)) is the mapping tool to convert CSV file to RDF. Please follow the Knowledge Graph Course Material provided by Institute of Data Science at Maastricht University ([Github repo](https://github.com/MaastrichtU-IDS/UM_KEN4256_KnowledgeGraphs)) to learn the convertion.

- Converted RDF data are uploaded to GraphDB triple store.

### Query publications using SPARQL

-  Example 1: Query the authors ordered by the number of their publications related to COVID-19

  ```SPARQL
  PREFIX pav: <http://purl.org/pav/> 
  select ?author (count(distinct ?paper) as ?count)  where { 
  	?paper pav:authoredBy ?author .
  } GROUP BY ?author
  ORDER BY DESC(?count)
  ```

- Example 2: Query all COVID-19 publications related to diabetes 

  ```SPARQL
  PREFIX covidPub: <http://covid19publication.org/keyword/>
  PREFIX dc: <http://purl.org/dc/elements/1.1/> 
  select ?paper ?title where { 
  	?paper dc:subject covidPub:diabetes .
      ?paper dc:title ?title . 
  }
  ```

  

