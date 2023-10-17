# Analyzing US Population and Wikipedia Articles for Cities

This repository encompases data and analysis for the project that emphasizes considering Bias in Data. The primary objective was to construct, analyze, and publish a dataset and answer the following:

1. The states with the greatest and least coverage of cities on Wikipedia compared to their population.
2. The states with the highest and lowest proportion of high quality articles about cities.
3. A ranking of US geographic regions by articles-per-person and proportion of high quality articles.

# Data Sources

## City Data
- Dataset: [us_cities_by_state_SEPT.2023.csv](./data/us_cities_by_state_SEPT.2023.csv)
- Description: List of Wikipedia article pages about US cities from each state.
- Source: The [Wikipedia Category:Lists of cities in the United States by state](https://en.wikipedia.org/wiki/Category:Lists_of_cities_in_the_United_States_by_state) was crawled to generate this data.

## Population Data
- Dataset: [NST-EST2022-ALLDATA.csv](./data/NST-EST2022-ALLDATA.csv)
- Description: Contains state population totals and components of change for years 2020 to 2022.
- Source: [State Population Totals](https://www.census.gov/data/tables/time-series/demo/popest/2020s-state-total.html)

## Regional Data
- Dataset: [US States by Region - US Census Bureau.xlsx](./data/US%20States%20by%20Region%20-%20US%20Census%20Bureau.xlsx)
- Description: Regional and divisional agglomerations as defined by the US Census Bureau.
- Source: US Census Bureau

# Data Considerations

The dataset is massive and has around 22000 articles that need to be analyzed. It takes a long time to access the API, hence the data acquisition process needs good error handling. The data has duplicate entries, which can be deleted by storing entries into a dictionary, which does not take duplicate keys. Population data contains non geographic locations,(US Census etc.) which I had to handle and remove separately. Additionally, From the Wikipedia list of US cities by State, no data can be found for the states Connecticut and Nebraska.

Here is a list of non-geographic locations present in the population data

| NAME                  | Population   |
|-----------------------|--------------|
| United States         | 333287557    |
| Northeast Region      | 57040406     |
| New England           | 15129548     |
| Middle Atlantic       | 41910858     |
| Midwest Region        | 68787595     |
| East North Central    | 47097779     |
| West North Central    | 21689816     |
| South Region          | 128716192    |
| South Atlantic        | 67452940     |
| East South Central    | 19578002     |
| West South Central    | 41685250     |
| West Region           | 78743364     |
| Mountain              | 25514320     |
| Pacific               | 53229044     |
| District of Columbia  | 671803       |
| Puerto Rico           | 3221789      |


# Code Process

The Project Process can be divided into two parts

## Data Gathering and Cleaning

Goal: The goal of this step is to gather data and create a dataset by merging the data sources mentioned above.

1. **Clone the Repository:**
2. **Create an account on [wikimedia](https://api.wikimedia.org/w/index.php?title=Special:UserLogin)**
3. **Generate a Personal API token by going to this [site](https://api.wikimedia.org/wiki/Authentication#Personal_API_tokens) and following the instructions**
4. **Add your email and token to the constants EMAIL and ACCESS_TOKEN respectively in the [notebook](./wikipedia_article_prediction.ipynb)**
5. **Run the Jupyter [Notebook](./wikipedia_article_prediction.ipynb):** 
6. **The notebook will call the endpoint https://en.wikipedia.org/w/api.php to fetch information about the article. From information it takes the latest revision id and calls the https://api.wikimedia.org/service/lw/inference/v1/models/enwiki-articlequality:predict to use [ORES](https://www.mediawiki.org/wiki/ORES) for predicting the article quality. Here is the article quality legend**

**Article Quality Legend:**

1. **FA** - Featured article
2. **GA** - Good article (sometimes called A-class)
3. **B** - B-class article
4. **C** - C-class article
5. **Start** - Start-class article
6. **Stub** - Stub-class article

**API calls are made with a use of multithreading for faster results. More about multithreading can be found [here](https://docs.python.org/3/library/concurrent.futures.html). API Calling logic is fail proof and can handle network related issues and stores the progress(the articles which are already processed)**

After successful completion of data gathering, we have a dataset with below schema

| Field               | Description                                      |
|---------------------|--------------------------------------------------|
| state               | US State                                         |
| regional_division   | Region and Division US State belongs to         |
| population          | Population of US State                           |
| article_title       | Title of Article on a city present in that state |
| revision_id         | Articles latest revision id                      |
| article_quality     | Predicted article quality by ORES                |


## Data Analysis Reproduction

**Goal**: Analyze the data generated in part 1 to analyze the relation between population and the Wikipedia article rating.

**Run the Jupyter [Notebook](./wiki_article_analysis.ipynb) to replicate the results of this project.**

# Research Implications


**Biases Expected and Found:**

Before starting the analysis, I anticipated biases in data representation favoring well-resourced states, assuming higher population and economic activity might lead to more and higher-quality articles. However, the data revealed an unexpected bias towards less populous states, challenging my initial assumptions about article coverage and quality.

**Identified Sources of Bias:**

The analysis brought to light biases in article quality per capita, favoring states with smaller populations. Additionally, major cities, expected to have better article quality due to higher immigrant populations, did not feature prominently in the top 10 high-quality coverage states. This disparity suggests editing patterns and content topics might influence article quality.

**Implications for Wikipedia as a Data Source:**

The results suggest that Wikipedia, while a vast and valuable data source, may exhibit biases in the representation of US states. The biases could stem from factors like editing dynamics, content focus, and population size, potentially impacting the reliability and completeness of data for certain regions.

**Potential for Biased Results in Data Science Research:**

In a realistic data science research scenario, using this data to train a model or perform hypothesis-driven research might result in biased or misleading outcomes. Algorithms trained on biased data could perpetuate the skewed representation of states, impacting any downstream analysis or decision-making processes.

**Appropriate Use of Data Despite Limitations:**

Despite its biases, this dataset can still be useful for research that does not heavily rely on proportional representation, such as sentiment analysis or topic modeling. It can provide valuable insights when the focus is on individual states rather than a comparative analysis based on population or economic metrics.

**Supplementation and Transformation for Bias Correction:**

Researchers can supplement this dataset by incorporating demographic data, editing frequency statistics, or considering diverse external sources to mitigate biases. Transforming the dataset through normalization techniques or using a more balanced dataset could help correct the biases observed, making the data more representative.

In summary, this analysis underscored the need for thorough consideration of biases when using Wikipedia data, especially regarding geographic representation. It emphasized the significance of acknowledging and addressing biases to ensure accurate and fair data-driven analyses.



# Wikipedia API Documentation and Licensing
For further information on Wikipedia API, refer to the [official documentation](https://wikimedia.org/api/rest_v1/) and [Terms and Conditions](https://www.mediawiki.org/wiki/API:REST_API#Terms_and_conditions).

# Licensing
The dataset is made available under the [Creative Commons License](https://creativecommons.org/licenses/by/4.0/).