# Designing a URL Shortening service like TinyURL
* Let's design a URL shortening service like TinyURL. This service will provide short aliases redirecting to long URLs. 
* Similar services: bit.ly, goo.gl, qlink.me, etc.

## 1. Why do we need URL shortening?
* URL shortening is used to create shorter aliases for long URLs. We call these shortened aliases “short links.” Users are redirected to the original URL when they hit these short links. Short links save a lot of space when displayed, printed, messaged, or tweeted. Additionally, users are less likely to mistype shorter URLs.

* For example, if we shorten this page through TinyURL: 
    * https://www.educative.io/collection/page/5668639101419520/5649050225344512/5668600916475904/
* We would get: 
    * http://tinyurl.com/jlg8zpc
* URL shortening is used for optimizing links across devices, tracking individual links to analyze audience and campaign performance, and hiding affiliated original URLs.

## 2. Requirements and Goals of the System
* Our URL shortening system should meet the following requirements:

### Functional Requirements:

* Given a URL, our service should generate a shorter and unique alias of it. This is called a short link.
* When users access a short link, our service should redirect them to the original link.
* Users should optionally be able to pick a custom short link for their URL.
* Links will expire after a standard default timespan. Users should be able to specify the expiration time.
### Non-Functional Requirements:

* The system should be highly available. This is required because, if our service is down, all the URL redirections will start failing.
* URL redirection should happen in real-time with minimal latency.
* Shortened links should not be guessable (not predictable).
### Extended Requirements:

* Analytics; e.g., how many times a redirection happened?
* Our service should also be accessible through REST APIs by other services.

## 3. Capacity Estimation and Constraints
Our system will be read-heavy. There will be lots of redirection requests compared to new URL shortenings. Let’s assume 100:1 ratio between read and write.

Traffic estimates: Assuming, we will have 500M new URL shortenings per month, with 100:1 read/write ratio, we can expect 50B redirections during the same period:

100 * 500M => 50B
What would be Queries Per Second (QPS) for our system? New URLs shortenings per second:

500 million / (30 days * 24 hours * 3600 seconds) = ~200 URLs/s
Considering 100:1 read/write ratio, URLs redirections per second will be:

100 * 200 URLs/s = 20K/s
Storage estimates: Let’s assume we store every URL shortening request (and associated shortened link) for 5 years. Since we expect to have 500M new URLs every month, the total number of objects we expect to store will be 30 billion:

500 million * 5 years * 12 months = 30 billion
Let’s assume that each stored object will be approximately 500 bytes (just a ballpark estimate–we will dig into it later). We will need 15TB of total storage:

30 billion * 500 bytes = 15 TB

Bandwidth estimates: For write requests, since we expect 200 new URLs every second, total incoming data for our service will be 100KB per second:

200 * 500 bytes = 100 KB/s
For read requests, since every second we expect ~20K URLs redirections, total outgoing data for our service would be 10MB per second:

20K * 500 bytes = ~10 MB/s
Memory estimates: If we want to cache some of the hot URLs that are frequently accessed, how much memory will we need to store them? If we follow the 80-20 rule, meaning 20% of URLs generate 80% of traffic, we would like to cache these 20% hot URLs.

Since we have 20K requests per second, we will be getting 1.7 billion requests per day:

20K * 3600 seconds * 24 hours = ~1.7 billion
To cache 20% of these requests, we will need 170GB of memory.

0.2 * 1.7 billion * 500 bytes = ~170GB
One thing to note here is that since there will be a lot of duplicate requests (of the same URL), therefore, our actual memory usage will be less than 170GB.

High level estimates: Assuming 500 million new URLs per month and 100:1 read:write ratio, following is the summary of the high level estimates for our service: