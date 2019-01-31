# Client Project: FEMA Power Outage Hotspot Tool
-----
### Problem Statement
The goal of this project is to locate power outages in the United States using social media data. Within this repository, you will find a documented process for sourcing, cleaning, modeling, and interpreting Twitter data in a process that accomplishes this goal. The models and methods contained here are intended for use by FEMA for disaster relief within the United States, but we invite and encourage any organization that provides emergency relief to freely make use of this repository.  

For any questions, please do not hesitate to contact us through LinkedIn:
- [Boom Devahastin Na Ayudhya](https://www.linkedin.com/in/boom-devahastin)
- [Chris Lee](https://www.linkedin.com/in/sung-won-lee-495149150)
- [Henry Blais](https://www.linkedin.com/in/henry-blais)

This project was completed in cooperation with [General Assembly](https://generalassemb.ly) in January 2019.

-----
### Project Files and Workflow
- [1 Scraping Twitter Data](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/1_Data-Collection.ipynb)
- [2 Data Cleaning Process](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/2_Data-Cleaning.ipynb)
- [3 Exploratory Data Analysis](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/3_Exploratory-Data-Analysis.ipynb)
- [4 Natural Language Processing and Modeling](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/4_Preprocessing-and-NLP-Modeling.ipynb)
- [5 Geographically Visualizing Power Outages](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/5_Geospatial-Visualizations.ipynb)
- [Presentation Slides](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/Presentation_Slides.pptx)

-----
# Executive Summary

Social Media data is an endless source of insight for data scientists, with a myriad of noble (or nefarious) applications.  Our team resolved to tap into this wealth of information and use it to create a tool to assist disaster relief efforts in the United States. However, a user may easily apply our method to any region of the world. 

In the remainder of this README file, we will outline the basic steps of our process:
1. Data Collection
2. Data Cleaning
3. Exploratory Data Analysis
4. Preprocessing and Modeling
5. Geospatial Visualizations

### 1. Data Collection
We resolved to use Twitter data for this version of this project.  In order to collect this data, we used a tried-and-true scraping method, purposely built into a scraping function for our data collection efforts in [1.0 Scraping Twitter Data](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/1_Data-Collection.ipynb).

We encountered avoidable setbacks that will be explained below.  Our scraper is an implementation of the excellent [twitterscraper](https://pypi.org/project/twitterscraper/0.2.7/) package, tuned to return a body of Tweets containing specific words or terms and posted within a specified time frame.  Both the keywords and time frame are specified as arguments of the twitterscraper.query_tweets function.

Our basic scraper relies on very few pre-filtering arguments and keywords by design.  In the future, it may be valuable to revise this and test the impact of tuning for more specific tweets, but this will be discussed later in "Proposed Next Steps". 

As previously alluded to, our team encountered scraping issues with [this](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/1_Data-Collection.ipynb) scraping function.  Specifically, we very quickly overtaxed the Twitter API and were suspended from pulling more tweets. While this should not be a major issue for a user who runs this scraper only once, if a user were to repeatedly execute it, say, in the process of refining the pre-filtering terms for the scraper, he/she would quickly be banned from acquiring new data so be warned.

### 2. Data Cleaning
In order to format our data into a useful and uniform set, we used a concise [cleaning process](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/2_Data-Cleaning.ipynb) and added some new data of our own.  

The most noteworthy development in our data cleaning deserves some explanation here. While working on this project, our team quickly learned that Twitter does NOT keep usable location data in their .html structure or .json output. While the Twitter API does include fields to store the city of origin, account owner location, and even latitude and longitude of the user at the time a tweet is posted, these fields are almost universally blank or unhelpful.  This is probably either because Twitter had deprecated location tracking or many users choose to opt out, or a combination of these and other factors. The location data was so incomplete in our scraped data that we are forced in this section to randomly assign location data as a proof-of-concept.  **Consequently, the conclusions and data drawn from this point forward are purely demonstrative and are not informed by actual geographical data**.  

The final step of our data cleaning process was to combine the scraped tweets and imputed cities with a data frame of all USA cities, including their coordinates, counties, etc. This data will help us in mapping and other efforts later on. 

### 3. Exploratory Data Analysis
Our [EDA](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/3_Exploratory-Data-Analysis.ipynb) file begins the process by:

1. Reformatting the text of all scraped tweets
    - Removal of unusual characters/punctuation
    - Handling misspelled words
    - Removing 'stopwords' (ie; he, the, it, is...) not helpful to text analysis
2. CountVectorizing the text column to facilitate term frequency analysis
3. Analyzing word frequency and use for further insights.

This process helped us to identify new keywords that might be helpful, eliminate some that were not, and pointed out some important improvements we could make in the future, which will be outlined later on.  

### 4. Preprocessing and Modeling
Our workflow is laid out and explained in great detail [here](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/4_Preprocessing-and-NLP-Modeling.ipynb). We strongly recommend reading through it on a step-by-step basis within that file as the process is very meticulously yet concisely explained there. Here we merely provide a brief overview of this process.

As discussed previously, our current working data does not include meaningful geographical information for individual tweets, which prevents us from empirically identifying tweets that occurred during blackouts. Without a confirmed test set containing this information, our modeling options were extremely limited.  

We opted for a natural language processing approach, namely one that resembles sentiment analysis, to tackle the classification problem. This revolved around the notion of cosine similarity of chosen keywords, associated with power outages from the tweets themselves.  In order to generate these metrics, we first tokenized the tweets title strings, then vectorized them using Word2Vec to create a usable numeric representation of those words that could be processed.  

Our model was able to generate meaningful predictions of power-outage-likelihood for each individual tweet based on the language and 'sentiment' used in the tweets themselves.  

### 5. Geospatial Visualizations
In this section, we used the geographic data we added and imputed in data cleaning, to generate heat maps that would help to identify "hotspots" of flagged power-outage related Twitter content. Our [mapping section](https://github.com/boom-deva/FEMA-Power-Outage-Hotspot-Detection/blob/master/5_Geospatial-Visualizations.ipynb) uses [Bokeh](https://bokeh.pydata.org/en/latest/), an excellent library for interactive visualizations. 

Bokeh was useful here because we were able to create an interactive map that we believe will be of more use to our users than a static plot might have been.  In future updates, we envision a re-arrangement of our program that would enable very local searches for power-outage hotspots (for instance, within a county).  In this framework, the interactive mapping we have implemented will be useful for exploring the shape and extent of identified power outages.

-----
### Proposed Next Steps
Unfortunately given the time constraint, what is contained in this repo represents only a current working model - a prototype of what we believe could be an immensely helpful tool for disaster relief groups.

First, let us take stock of what our program does currently.  Front-to-end, this program scrapes a large body of Tweets, compares their language with a list of terms we generated that relate to power outages, and then classifies the Tweets in that body into 2 groups: 'power outage' and 'irrelevant' using NLP. While sound, the process suffers from a number of shortcomings that can be addressed through a series of proposed updates below. We offer the current version of this repository as a sort of sample/proof-of-concept that can be built out into a much more robust tool with the following additions:

#### A. General
All the steps we have taken and outlined above are intended to create a working model of Twitter data. This is a desirable goal of course, but that goal was undermined by the absence of location data within Twitter's API. 

Furthermore, generating that model is only an intermediate step towards the finished product we hope to build. In our finished product, we will generate a supervised, rather than unsupervised, learning model that can accurately differentiate tweets originating from blacked-out cities and neighborhoods.  This will require past tweets that we can confirm originated in blackout zones, which is an attainable goal (more on this below).  

Rather than stopping at this point, the model described above will become the working core that enables us to build on more useful features.

#### B. Restructure of Scraping Function / Implement Core Model
The revised model proposed in "Next Steps: A" will require a large initial scrape of Twitter data, ideally in the thousands of posts, if not more.  This body of Tweets will benefit from improvements to our scraping methods, which we will outline below.  

The large initial scrape ('Scrape A') will not be limited by keywords in the future, but will instead depend entirely on windows of time which we will procedurally generate.  These windows of time will correspond to the start time and duration of known past power outages throughout the United States. This data is easily available through the [websites](https://apps.coned.com/stormcenter/external/default.html) of all major power providers by law.  

The revised scraper will pull Tweets only during periods where there is one or more active power outage in the United States (we would most likely filter these outage periods to exclude outages with low numbers of affected people, another statistic tracked in the link above), so that we get a mixture of tweets from affected areas ('Targets') and from non-affected areas ('Non-Targets'). That data will be used to build a core dataset and core model ('Core') that later steps will rely on.  

#### C. Establishing Location to Enable Supervised Learning
There are some simple steps we can take to ensure the data we are working with comes from a known location.  The first, best, and most obvious one comes straight out of the social media playbook: we sit back and let Twitter users identify themselves for us.  

This proposed feature is an upgrade to the Scrape A above, as well as to smaller scrapes that we will propose and discuss below.  Since there is no shortage of Tweets on out there on the internet, we can afford to be selective about which ones we scrape.  

What we could do is add on a second and completely separate pre-filtering rule for all scraping functions moving forward, so that a City name AND one of our keywords becomes the query for our scraper.  

This would limit all returned tweets to those that are the most likely to self-identify their location. For instance, rather than getting huge numbers of Tweets such as

(1) *"Oh man my power is out! Lame!"*

we would instead probably get a body of tweets reading more to the tune of 

(2) *"Big power outage in Boston tonight, I can't watch the game!"*

or even

(3) *"Boston lost power today, serves them right, lousy Red Sox"*

We see that (1) tells us nothing about its origin, rendering it useless. However (2) self-identifies location, and (3) refers us to a remote blackout location that the user knows of. In either case, we have a lot more to go on that we started with.

From here, we can actually do a lot more to confirm the location of a referenced outage.  Specifically, we can use some of Twitter's helpful metadata: the "User Time Zone" and "Time Posted" portions. If we include these as features in our scraped data (encoded as UNIX timestamps for easier comparison), then we can compare Tweets to known prior blackouts and examine whether blackout keywords spike in those areas during said known blackouts. This will allow us to engineer many useful features, including better predictive terminology, measurements of how accurately users are when they self-identifying their location and more.  

#### D. Small Scale Scraping and LocalSearch
Everything we have discussed so far contributes to building a progressively more effective core model. However, a caveat is all that has been outlined relies on historical data, which clearly is not helpful to a disaster relief group. 

The features we've discussed will allow us to build a very accurate core model, which will be instrumental for the next proposed addition, which we call LocalSearch.  LocalSearch is the best tool to addresses the core goal of this project, which is to identify and locate the vicinity of blackouts in as close to real-time as possible.  

LocalSearch is by design a miniaturized version of the function that generated data to fuel our core model, i.e. by generating an identically formatted but new dataset on the fly, we are essentially creating a ($\text{x_{test}}$, $\text{y_{test}}$) to correspond to the pre-existing data ($\text{x_{train}}$, $\text{y_{train}}$) used by Core.  

LocalSearch would consequently

1. Take an argument designating a city or list of cities to filter Tweets
2. Continue to use the same keyword filtering as Core
3. Scrape a much smaller dataset at a rate that complies with Twitter's API rules so this tool remains usable and does not abuse Twitter's constraints, e.g. 1,000 tweets
4. Use a NLP classification model to identify tweets that we believe are originating from power outage afflicted areas using the information learned in Core.
5. Generate a Bokeh interactive heatmap of the searched city and its surroundings, and output this map along with summary data from the modeling results.  

-----
# Conclusion
Social media is an astonishingly rich source of data, but some small quirks (or in this case, justifiable privacy measures) can limit the scope of its potential.  In this case, the impact of the near-universally empty "User Location" in Twitter metadata was a major obstacle as the location was by far the most desirable piece of information we could have received.  

This setback severely limited the degree to which we could implement a finished version of our product, i.e. LocalSearch as outlined above. However, with some creativity and a client-funded subscription to Twitter's Premium API, we have demonstrated that it is possible to infer user location for our purposes.

Ultimately, it is clear that social media can be used to pinpoint power outages in the United States.  We hope that you will consider our proof-of-concept model alongside our proposals for a finished product and bear in mind that building out the proposed features will be an ongoing and laborious process.  Thank you for reading about our project.