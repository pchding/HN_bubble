## News Bubble

Most recommendation engines try to discover users' preferences to give them the content that is similar to what they already like. This practice has created the so called "Filter Bubble", which separate users from information that disagrees with their viewpoints, and effectively isolate them in their own cultural or ideological bubbles. 

In this project, I propose a new kind of recommendation engine that aims to news post that is differnt from most other posts under similar topic. The news source I use is Hacker News, a social news website focusing on computer science and entrepreneurship. Users of the website can submit news post (usually with some external link) to the websire for others to see, upvote and comment. However, Hacker News does not provide a topic categorization system (only posts concerning job posting, showcasing one's own work and questions to the community are categorized). With more than 500 posts every day, it has become hard to find posts that should interest you.

The project contains two part. In the first part, I build a topic categorization system based on post title, content and linked url. The topic categorization is done using LDA. The second part is an autoencoder based model to find posts in each topic groups that stand out. These stand out posts are considered different from most of the other posts under the same topic. They might be about new ideas or tools that are not well accepted yet (they may be rough around the edges, but may have strong potential to change the status quo). And due to this, they may receive few upvotes from other users and are thus more difficult to find. 

### Data Source

Hacker News database is acquired through BigQuery Api. I use 6 years of posts from 2013-06 to 2019-06. Newer posts are reserved for future validation. As a first model, I didn't include comment text for the analysis. 

### Topic Modelling

Text is first cleaned to remove punctuations and stopwords. It is also lemminated with wordnet lemminiazer. Different from previous analysis, I have also included linked external url for the analysis.  Also I only included the main domain name (like bbc, bloombery) in the text. As can be seen from the plot, the top referenced websites have many differences with each other, some are general news site, some are open source repos, some are focused on entrepreneurship, and some on consumer electronics. I believe including these site names will help for the topic modelling, since many of these websites focus on a sub set of topics.

![topics](/images/topsites.png)

In th first model I set the number of topics to 15. Results can be seen in the following graph.

{% include lda.html %}

We see some of the topics still overlapped too much, indicating that we may need to set a hihger topic number for the model. However, these overlapping topics seem to have relatively small counts of posts, we may need to find more trainning data (I believe adding posts from some subreddit may be a good idea). 

Another observation is that, some websites names do appear with quite high frequency in some of the topics (like Github for the open source development topic, facebook and google for the web security and privacy topic, Bloomberg for the machine learning topic, youtube for the entertainment topic). This indicates that adding domain names to the post text is indeed helpful.

### Next Steps
1. Find optimal number of topics for the topic model
2. Add reddit posts under certain subreddits to help topic model trainning.
2. Incorporate top (according to upvotes) comment under one topic to the correspoinding topic text for the topic modelling
3. Build the auto-encoder model to detect anomaly and make recommendations under certain topic
