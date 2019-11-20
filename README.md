## News Bubble

Most recommendation engines try to discover users' preferences to give them the content that is similar to what they already like. This practice has created the so called "Filter Bubble", which separate users from information that disagrees with their viewpoints, and effectively isolate them in their own cultural or ideological bubbles. 

In this project, I propose a new kind of recommendation engine that aims to find news post that is different from most other posts under similar topic. The news source I use is Hacker News, a social news website focusing on computer science and entrepreneurship. Users of the website can submit news post (usually with some external link) to the website for others to see, upvote and comment. However, Hacker News does not provide a topic categorization system (only posts concerning job posting, showcasing one's own work and questions to the community are categorized). With more than 500 posts every day, it has become hard to find posts that should interest you.

The project contains two part. In the first part, I build a topic categorization system based on post title, content and linked url. The topic categorization is done using LDA. The second part is an autoencoder based model to find posts in each topic groups that stand out. These stand out posts are considered different from most of the other posts under the same topic. They might be about new ideas or tools that are not well accepted yet (they may be rough around the edges, but may have strong potential to change the status quo). And due to this, they may receive few upvotes from other users and are thus more difficult to find. 
 
### Data Source

Hacker News database is acquired through BigQuery Api. I use 6 years of posts from 2013-06 to 2019-06. Newer posts are reserved for future validation. As a first model, I didn't include comment text for the analysis. 

### Topic Modelling

Text is first cleaned to remove punctuations and stopwords. It is also lemminated with wordnet lemminiazer. Different from previous analysis, I have also included linked external url for the analysis.  Also I only included the main domain name (like bbc, bloomberg) in the text. As can be seen from the plot, the top referenced websites have many differences with each other, some are general news site, some are open source repos, some are focused on entrepreneurship, and some on consumer electronics. I believe including these site names will help for the topic modelling, since many of these websites focus on a sub set of topics.

![topics](/images/topsites.png)

In this first model I set the number of topics to 15. Results can be seen in the following graph.

{% include lda.html %}

We see some of the topics still overlapped too much, indicating that we may need to set a higher topic number for the model. However, these overlapping topics seem to have relatively small counts of posts, we may need to find more training data (I believe adding posts from some subreddit may be a good idea). 

Another observation is that, some websites names do appear with quite high frequency in some of the topics (like Github for the open source development topic, facebook and google for the web security and privacy topic, Bloomberg for the machine learning topic, youtube for the entertainment topic). This indicates that adding domain names to the post text is indeed helpful.

### Auto-encoder Recommendation
For the first draft, I made a very simple auto-encoder model with a single layer encoder and decoder. I have first collected all the posts in topic 6 (job, entrepreneurship) and converted each post title to a 1*300 vector (obtained by finding the embeddings of each word in the glove.840B.300d dataset and taking the mean of all the words in the title ). I then trained the auto-encoder with these average embeddings as input and target. 

From the the predicted values, I find the ones that are more different from the original input and then find their corresponding titles. The top ten results can be seen in the following table.

![aerec](/images/aeout.png)

As we can see here, many of these posts are low quality posts that has no meaningful title. However, two of them stands out, one is about Adidas buying training app Runtastic and the other one is about a security enhanced router released by Bitdefender, an anitvirus software company. If we ignore the meaningless post, this simple autoencoder has actually done a good enough for picking out unusual posts related to entrepreneurship. 

Further improvement can come from two areas: 1. Filter out the low quality posts by another auto-encoder model trained on the whole dataset. 2. Implement a LSTM-base auto-encoder model to preserve more information contained in the texts.

### Next Steps
1. Find optimal number of topics for the topic model
2. Add reddit posts under certain subreddits to help topic model training.
2. Incorporate top (according to upvotes) comment under one topic to the corresponding topic text for the topic modelling
3. Filter out low quality posts by another auto-encoder model trained on the whole dataset.
4. Build the LSTM based auto-encoder model to detect anomaly and make recommendations under certain topic
