---
name: News Aggregator 
tools: [Topic Modeling, Machine Learning, Express.js, Scrapy, FastAPI, MongoDB, ReactJS]
image: /assets/news-aggregator-thumb.png
description: A web application leveraging Topic Modeling (an unsupervised NLP method) to cluster Nepali news articles and identify the most significant, interpretable keywords for each cluster.
# external_url: https://github.com/wanderer056/Virtual-Whiteboard
---

# News Aggregator

**News Aggregator** is a web application that scrapes Nepali news articles from multiple news sources (currently [OnlineKhabar](https://www.onlinekhabar.com/) and [Annapurna Post](https://www.annapurnapost.com/)) and employs topic modeling techniques to group similar articles by their headlines while providing meaningful topics for the clusters.

![preview](/assets/frontpage.png)

{% include elements/button.html link="https://nepnews-aggregator-2plic.ondigitalocean.app/" text="View Live App" block=true %}

---

## Features and Workflow

1. **Web Scraping**  
   - Built using Scrapy and deployed to [Zyte](https://www.zyte.com/scrapy-cloud/).  
   - The scraper initially gathers a comprehensive dataset of news articles, including the headline, publication date, and link, which is stored in MongoDB.  
   - It can also scrape new articles at regular intervals for real-time updates.

2. **Topic Modeling Pipeline**  
   The pipeline for clustering and topic extraction is powered by [BERTopic](https://maartengr.github.io/BERTopic/index.html).  
   
   - **Embeddings Generation**  
     - Initial trials with pre-trained sentence transformers produced suboptimal embeddings for the Nepali language.  
     - Switching to the pre-trained Nepali model in **FastText**, a word embeddings library by Facebook AI, yielded better results for representing headlines.  

   - **Dimensionality Reduction**  
     - To address the high dimensionality of embeddings, **UMAP** (Uniform Manifold Approximation and Projection) was used, enabling more effective clustering.  

   - **Clustering**  
     - Clustering of reduced embeddings was achieved using **HDBSCAN**, a density-based clustering algorithm suitable for complex datasets like news headlines.  

   - **Topic Extraction**  
     - A bag of words was created by lemmatizing and removing stop words from each cluster of news headlines.  
     - **c-TF-IDF** was applied to determine the significance of words in each cluster, considering inter-cluster differences. This process identified the top `n` keywords, which were then used to assign topics to clusters.

   - **Cluster Prediction**  
     - For incoming news articles, **cosine similarity** was used to classify them into pre-existing clusters, enhancing the app's ability to handle new data seamlessly.  

 3. **Frontend and Backend Integration**
    - The **frontend** of the application is built using **ReactJS**.  
    - The **backend** is primarily developed using **Express.js**, managing APIs, server-side operations, and database interactions.  
    - **FastAPI** is used to build APIs for topic model predictions, integrating the BERTopic model for real-time cluster assignment of new articles.  
    - **MongoDB** serves as the database, storing scraped news articles and clustering results efficiently.

---

### Visualizations

- **Clusters Visualization**  
  {% include elements/figure.html image="/assets/clusters.png" caption="Cluster Formed" %}

  <p class="text-center">
  {% include elements/button.html link="https://nepnews-aggregator-2plic.ondigitalocean.app/topic_map.html" text="Interactable Visualization of Clusters" style="primary" size="sm" %}
  </p>

- **Intertopic Distance Map**  
  {% include elements/figure.html image="/assets/intertopic-distance-map.png" caption="Intertopic Distance Map" %}

  <p class="text-center">
  {% include elements/button.html link="https://nepnews-aggregator-2plic.ondigitalocean.app/visual.html" text="Interactable Intertopic Distance Map" style="primary" size="sm" %}
  </p>

---

## Source Code and Components

{% capture list_items %}
View Scraper Source Code,https://www.github.com
View Topic Modeling Source Code,https://www.github.com
View Express.js Backend Code, https://www.github.com
View Frontend ReactJS Code, https://www.github.com
Topic Coherence code (FastAPI), https://www.github.com
{% endcapture %}
{% include elements/list.html title="Source Codes" %}

