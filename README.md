## News Bubble

Most recommendation engines try to discover users' preferences to give them the content that is similar to what they already like. This practice has created the so called "Filter Bubble", which separate users from information that disagrees with their viewpoints, and effectively isolate them in their own cultural or ideological bubbles. 

In this project, I propose a new kind of recommendation engine that aims to news post that is differnt from most other posts under similar topic. The news source I use is Hacker News, a social news website focusing on computer science and entrepreneurship. Users of the website can submit news post (usually with some external link) to the websire for others to see, upvote and comment. However, Hacker News does not provide a topic categorization system (only posts concerning job posting, showcasing one's own work and questions to the community are categorized). With more than 500 posts every day, it has become hard to find posts that should interest you.

The project contains two part. In the first part, I build a topic categorization system based on post title, content and linked url. The topic categorization is done using LDA. The second part is an autoencoder based model to find posts in each topic groups that stand out. These stand out posts may represent a break outside of the bubble the reader needs.

### Data Source

Hacker News database is acquired through BigQuery Api. I use 6 years of posts from 2013-06 to 2019-06. As a first model, I didn't include comment text for the analysis. Different from previous analysis, I have also included 

### Topic Modelling

Text is first cleaned to remove punctuations and stopwords. It is also lemminated with wordnet lemminiazer. Dif

### Auto-encoder Recommendation
