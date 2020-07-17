<h1>Presedential Election 2017 in France: Twitter Analysis</h1>

In 2017, Emmanuel Macron and Marine Le Pen were the final two candidates in the French Presidential Election. The two candidates had drastically different approaches to governing, and as such, the election was a major topic of discussion on Twitter.


<p align="center"><img width=86% src=https://cdn.vox-cdn.com/thumbor/BmU5Qs8Y88f5QmvZ6RxELTAjh4I=/25x0:775x500/1200x800/filters:focal(25x0:775x500)/cdn.vox-cdn.com/assets/1063381/twitterfrance.jpg> 
  
<br>

## Table of Content

- [Objective](#objective)
- [Approach](#approach)
- [Tweet Analysis](#tweet-analysis)


<br>

## Objective 

The objective of this case study was to analyzie twitter discussions happening in France in time before the election to compare popularity of both candiates, and to point media influencers. 

<br>

## Approach

- Use data gathered from Twitter API
- Process data in Spark  
- Visualize insights with Python library Matplotlib

<br>

## Tweet Analysis

To load data, we feed .json file into Spark docker container: 

```

spark_df = spark.read.json('./data/french_tweets.json')

```

Our DataFrame has quite comples structure. Below is an example of first 50 lines (out of 608):

```

root
 |-- contributors: string (nullable = true)
 |-- coordinates: struct (nullable = true)
 |    |-- coordinates: array (nullable = true)
 |    |    |-- element: double (containsNull = true)
 |    |-- type: string (nullable = true)
 |-- created_at: string (nullable = true)
 |-- display_text_range: array (nullable = true)
 |    |-- element: long (containsNull = true)
 |-- entities: struct (nullable = true)
 |    |-- hashtags: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- indices: array (nullable = true)
 |    |    |    |    |-- element: long (containsNull = true)
 |    |    |    |-- text: string (nullable = true)
 |    |-- media: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- display_url: string (nullable = true)
 |    |    |    |-- expanded_url: string (nullable = true)
 |    |    |    |-- id: long (nullable = true)
 |    |    |    |-- id_str: string (nullable = true)
 |    |    |    |-- indices: array (nullable = true)
 |    |    |    |    |-- element: long (containsNull = true)
 |    |    |    |-- media_url: string (nullable = true)
 |    |    |    |-- media_url_https: string (nullable = true)
 |    |    |    |-- sizes: struct (nullable = true)
 |    |    |    |    |-- large: struct (nullable = true)
 |    |    |    |    |    |-- h: long (nullable = true)
 |    |    |    |    |    |-- resize: string (nullable = true)
 |    |    |    |    |    |-- w: long (nullable = true)
 |    |    |    |    |-- medium: struct (nullable = true)
 |    |    |    |    |    |-- h: long (nullable = true)
 |    |    |    |    |    |-- resize: string (nullable = true)
 |    |    |    |    |    |-- w: long (nullable = true)
 |    |    |    |    |-- small: struct (nullable = true)
 |    |    |    |    |    |-- h: long (nullable = true)
 |    |    |    |    |    |-- resize: string (nullable = true)
 |    |    |    |    |    |-- w: long (nullable = true)
 |    |    |    |    |-- thumb: struct (nullable = true)
 |    |    |    |    |    |-- h: long (nullable = true)
 |    |    |    |    |    |-- resize: string (nullable = true)
 |    |    |    |    |    |-- w: long (nullable = true)
 |    |    |    |-- source_status_id: long (nullable = true)
 |    |    |    |-- source_status_id_str: string (nullable = true)
 |    |    |    |-- source_user_id: long (nullable = true)
 |    |    |    |-- source_user_id_str: string (nullable = true)
 |    |    |    |-- type: string (nullable = true)
 |    |    |    |-- url: string (nullable = true)
 |    |-- symbols: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- indices: array (nullable = true)
 
 
 ```
# Which candidate is more popular?

For analysis, we first wanted to see who was mentioned more often - Emmanuel Macron or Marine Le Pen. 
We used the following code:

```
#counting the number of original tweets mentioning names of candidates:
origins = (spark_df.select('retweeted', 'text')
           .where((spark_df.retweeted == 'false')&(spark_df.text.contains('EmmanuelMacron'))))
origins.count()

```
We got this numbers:
- Emmanuel Macron: 2 185 mentions (63%)
- Marine Le Pen: 1 279 mentions (36%)

Since the elections are in the past, we can compare our numbers to the voting results:


<p align="center"><img width=86% src=https://ichef.bbci.co.uk/news/976/cpsprodpb/132BE/production/_95962587_french_election_results.jpg> 
  
  

# Who else were mentioned

For the next step, we wanted to find out who else were mentioned often in tweets. For that, we used RDD file:

```
user_mentions = (tweets_rdd.flatMap(lambda row: entity_fix(row))
              .filter(lambda empty: (empty != []) and (empty != 'x'))
              .map(lambda name: name['screen_name']))
```


