## Motivation

Most recommendation engines try to discover users' preferences to give them the content that is similar to what they already like. This practice has created the so called "Filter Bubble", which separate users from information that disagrees with their viewpoints, and effectively isolate them in their own cultural or ideological bubbles. 

In this project, I propose a new kind of recommendation engine that aims to find news post that is different from most other posts under similar topic. The news source I use is Hacker News, a social news website focusing on computer science and entrepreneurship. Users of the website can submit news post (usually with some external link) to the website for others to see, upvote and comment. However, Hacker News does not provide a topic categorization system (only posts concerning job posting, showcasing one's own work and questions to the community are categorized). With more than 500 posts every day, it has become hard to find posts that should interest you.

The project contains two part. In the first part, I build a topic categorization system based on post title, content and linked URL. The topic categorization is done using both LDA and BERT embeddings. The second part is an autoencoder based model to find posts in each topic groups that stand out. These stand out posts are considered different from most of the other posts under the same topic. They might be about new ideas or tools that are not well accepted yet (they may be rough around the edges, but may have strong potential to change the status quo). And due to this, they may receive few upvotes from other users and are thus more difficult to find. 
 
### Data Source

Hacker News database is acquired through BigQuery Api. I use 6 years of posts (around 2 Million posts). Newer posts are reserved for future validation. As a first model, I didn't include comment text for the analysis. 

### Topic Modeling

#### LDA

Text is first cleaned to remove punctuation, special characters, HTML tags, non ASCII characters, and stopwords. It is also lemmatized with Spacy. Different from previous analysis, I have also included linked external url for the analysis.  Also I only included the main domain name (like BBC, Bloomberg) in the text. As can be seen from the plot, the top referenced websites have many differences with each other, some are general news site, some are open source repos, some are focused on entrepreneurship, and some on consumer electronics. I believe including these site names will help for the topic modeling, since many of these websites focus on a sub set of topics.

![topics](/images/topsites.png)

In this first model I set the number of topics to 15. Results can be seen in the following graph. An interesting observation is that, some websites names do appear with quite high frequency in some of the topics (like GitHub for the open source development topic, Facebook and google for the web security and privacy topic, Bloomberg for the machine learning topic, Youtube for the entertainment topic). This indicates that adding domain names to the post text is indeed helpful.

{% include lda.html %}

We see that the separation of topics is not ideal. The main reason for this is that each post contain quite few words and algorithms that rely on co-occurrence like LDA does perform well for short text classification.

#### Incorporating BERT Embedding

One way to solve the above problem is to better incorporate meanings into the post representations. Modern language models provide us the means to do this. We can get a 768 element sentence embedding for each post by running them through a pre-trained BERT model. These fixed length embeddings contain the contextual information of how these posts are related to each other to the larger corpus(Wikipedia+BookCorpus) that the model is trained on and then can then used as the input for topic classification. However, the word co-occurrence information that can be unearthed by LDA may still be useful in this case, especially when certain topics naturally associate with certain jargon. The problem is that "There is no standard way of combining topics with pretrained contextual representations such as BERT" (Peinelt, et al. "tBERT: Topic Models and BERT Joining Forces for Semantic Similarity Detection." Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics. 2020)

For the first model, I simply concatenated the LDA output (topic distribution of the posts) and BERT embeddings (for better performance on 2 Million posts, DistilBERT was used). After that, for better clustering performance, I used an auto-encoder to reduce dimensions the concatenated vector and the reconstruct them. The middle layer output is then taken out to use as the dimension-reduced representation of the LDA+BERT vector.

Unsupervised clustering is then applied on this vector. After getting several topic clusters, the next step is to determine what these topics are talking about. A quick application of TF-IDF is enough to find the key words of each topic. 

Following is some preliminary results.

### Auto-encoder Recommendation

I built another two auto-encoders in this section. They are different from the previous auto-encoder in that they utilize word-level embeddings instead of sentence embeddings. I have found they outperform the previous one in terms of finding unusual posts, possibly since word-level preserves more information.

To make better use of the word-level embeddings and deal with the long text sequences, I used LSTM and CNN network structures for the auto-encoder model. After the models are trained, I then find the posts that have larger reconstruction error, and these are the unusual posts that the model would recommend.

However, there are some lower quality posts (like advertisements) in the results. To further improve model performance, an auto-encode should be built on the whole dataset, and filter out these posts before doing topic modeling and recommendation. 

Another observation is that LSTM and CNN auto-encoders perform similarly on this task, although CNN ones paralleled much better are faster to run.

### Next Steps
1. Explore other methods to combine word-occurrence information with contextual embeddings
1. Manually tag some posts and employ semi-supervised learning framework for topic categorization.
2. Add reddit posts under certain subreddits to help topic model training.
2. Incorporate top (according to upvotes) comment under one topic to the corresponding topic text for the topic modeling