# System-Design

## Step 1 Requirement Clarifications
* It is always a good idea to ask questions about the exact scope of the problem we are solving. Design questions are mostly open-ended, and they don’t have ONE correct answer, that’s why clarifying ambiguities early in the interview becomes critical. Candidates who spend enough time to define the end goals of the system always have a better chance to be successful in the interview. Also, since we only have 35-40 minutes to design a (supposedly) large system, we should clarify what parts of the system we will be focusing on.

* Let’s expand this with an actual example of designing a Twitter-like service. Here are some questions for designing Twitter that should be answered before moving on to the next steps:
    * Will users of our service be able to post tweets and follow other people?
    * Should we also design to create and display the user’s timeline?
    * Will tweets contain photos and videos?
    * Are we focusing on the backend only or are we developing the front-end too?
    * Will users be able to search tweets?
    * Do we need to display hot trending topics?
    * Will there be any push notification for new (or important) tweets?

## System Interface Definition
* Define what APIs are expected from the system. This will not only establish the exact contract expected from the system, but will also ensure if we haven’t gotten any requirements wrong. Some examples for our Twitter-like service will be:

```s
postTweet(user_id, tweet_data, tweet_location, user_location, timestamp, …)  
generateTimeline(user_id, current_time, user_location, …)  
markTweetFavorite(user_id, tweet_id, timestamp, …)  
```