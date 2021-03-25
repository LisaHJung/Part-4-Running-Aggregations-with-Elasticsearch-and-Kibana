# Beginner's Crash Course to Elastic Stack Series
## Part 4: Running Aggregations with Elasticsearch andKibana
Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 4: Running Aggregations with Elasticsearch and Kibana.

Workshop objectives:
- run metric aggregations
- run buckets aggregations
- combined aggregations

## Resources

[Beginner's Crash Course to Elastic Stack Table of Contents](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series) This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://ela.st/elastic-beginners)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Presentation]()

[Dataset](https://www.kaggle.com/carrie1/ecommerce-data) from Kaggle used for workshop

[Elastic America Virtual Chapter](https://community.elastic.co/amer-virtual/): Want to attend live workshops? Join the Elastic Americal Virtual Chapter to get the deets!

## Review from Workshop Part 3
There are two main ways to search in Elasticsearch:
1) `Queries`retrieve documents that match the specified criteria. 
2) `Aggregations` present the summary of your data as metrics, statistics, and other analytics.  

### Get information about documents in an index
The following query will retrieve all documents that exist in the specified index. This query is a great way to explore the structure and content of your document. 

Syntax: 
```
GET Enter_name_of_the_index_here/_search
```
Example: 
```
GET e_commerce/_search
```
Expected response from Elasticsearch:

Elasticsearch displays a number of hits and a sample of 10 search results by default.  The field "_ source"(line 22) lists all fields or content of the document.

![image](https://user-images.githubusercontent.com/60980933/112375185-9c52d280-8ca8-11eb-9952-16f24171dfbd.png)

### Aggregations Request Syntax
Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "aggs" or "aggregations": {
    "Name your aggregation here": {
      "Specify aggregation type here": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```

### Metric Aggregations 
#### Analyze the data to get the `sum` of all unit prices in the data set

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "aggs" or "aggregations": {
    "Name your aggregation here": {
      "sum": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```
Example:
```
GET e_commerce/_search
{
  "aggs": {
    "sum_unit_price": {
      "sum": {
        "field":"UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

By default, Elasticsearch will return top 10 most relevant documents. 

![image](https://user-images.githubusercontent.com/60980933/112508582-4d16ab80-8d55-11eb-93c1-09e956a14aaf.png)

When you minimize hits(red box- line 10), you will see the aggregations report we named sum_unit_price(image below). This report displays the sum of all unit prices listed in our data set. 

![image](https://user-images.githubusercontent.com/60980933/112512282-da0f3400-8d58-11eb-892d-0f577c9247fd.png)

If the purpose of running an aggregation is solely to get the values of aggregations, you can add the size parameter and set it to 0 as shown below. This parameter will prevent Elasticsearch from fetching the top 10 documents and speed up the query. 

Example:
```
GET e_commerce/_search
{
  "size": 0,
  "aggs": {
    "sum_unit_price": {
      "sum": {
        "field":"UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

Now you do not need to minimize hits to get to the aggregations report! We will be adding the size parameter to all aggregations request from this point on. 

![image](https://user-images.githubusercontent.com/60980933/112512473-032fc480-8d59-11eb-8c42-d3cfa7d23632.png)

#### Analyze the data to show the lowest(`min`) unit price of an item 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs" or "aggregations": {
    "Name your aggregation here": {
      "min": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```
Example: 
```
GET e_commerce/_search
{
  "size": 0,
  "aggs": {
    "lowest_unit_price": {
      "min": {
        "field":"UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

The lowest unit price of an item is 1.01. 
![image](https://user-images.githubusercontent.com/60980933/112509885-869be680-8d56-11eb-9c2e-5935ff7437e8.png)

#### Analyze the data to show the highest(`max`) unit price of an item 
Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs" or "aggregations": {
    "Name your aggregation here": {
      "max": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```
Example: 
```
GET e_commerce/_search
{
  "size": 0,
  "aggs": {
    "highest_unit_price": {
      "max": {
        "field":"UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:
The highest unit price of an item is 498.79. 

![image](https://user-images.githubusercontent.com/60980933/112511189-cca57a00-8d57-11eb-9ab3-809b2a410636.png)

#### Analyze the data to show the `average` unit price of items in the inventory 
Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs" or "aggregations": {
    "Name your aggregation here": {
      "avg": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```
Example: 
```
GET e_commerce/_search
{
  "size": 0,
  "aggs": {
    "average_unit_price": {
      "avg": {
        "field":"UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch: 

Average unit price in our invetory is around 4.39.

![image](https://user-images.githubusercontent.com/60980933/112511759-58b7a180-8d58-11eb-811f-8d6cb852c220.png)

#### `Stats` Aggregation: Analyze the data to show all the main metrics(count, min, max, avg, sum) in one request. 
Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs" or "aggregations": {
    "Name your aggregation here": {
      "stats": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```
Example:
```
GET e_commerce/_search
{
  "size": 0,
  "aggs": {
    "all_stats_unit_price": {
      "stats": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected Response from Elasticsearch:

Stats aggregation will yield the values of `count`(total number of unit prices in the data set), `min`, `max`, `avg`, and `sum`(sum of all unit prices in the data set). 

![image](https://user-images.githubusercontent.com/60980933/112512994-84875700-8d59-11eb-938a-fb7c61aee2b2.png)

#### The Percentiles Aggregations
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "unit_price_percentiles": {
      "percentiles": {
        "field":"UnitPrice",
        "percents": [
          25,
          50,
          75
        ]
      }
    }
  }
}
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/112379835-3e28ee00-8cae-11eb-8d01-dda32a3960f3.png)

#### The Cardinality Aggregations
The cardinality aggregation calculates the count of distinct values for a given field. You should be aware that this count is approximate. 

Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "number_of_customers": {
      "cardinality": {
        "field":"CustomerID"
      }
    }
  }
}
```
Expected response from Elasticsearch: 
![image](https://user-images.githubusercontent.com/60980933/112384174-b34af200-8cb3-11eb-8d4c-1eb3f068f81f.png)

### Aggregations Scope
#### Analyze the data to show the the sum of number of items sold in France

In the previous example, the aggregation was run on all the documents of the indices matching the index ecommerce_data. What happens if you want to run an aggregation on just a subset of the documents? You can add a query clause to an aggregation to limit the scope of the query. The metric will then be computed on the set of documents defined by your query.

For example, to answer the question: “What is the sum of number of items sold in France?” you need to combine a query and an aggregation.

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "query": {
    "Enter match or match_phrase here": { "Enter the name of the field": "Enter the value you are looking for" }
  },
  "aggregations": {
    "Name your aggregation here": {
      "Specify aggregation type here": {
        "field": "Name the field you want to aggregate here",
        "size": State how many buckets you want returned here
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "query": {
    "match": {
      "Country": "France"
      
    }
  },
  "aggs": {
    "france_total_number_of_items_sold": {
      "sum": {
        "field":"Quantity"
      }
    }
    
  }
}
```
###  Searching for a phrase
#### What happens when you use the `match query` to search for phrases?
Let's search for articles about Ed Sheeran's song "Shape of you" using the match query.

Example: 
```
GET news_headlines/_search
{
  "query": {
    "match": {
      "headline":{
        "query":"Shape of you"
   }
  }
 }
}
```
Expected response from Elasticsearch:

Elasticsearch returns greater than 10,000 hits. The top hit as well as many others in the search results only contain the search terms "you" and "shape". These terms are not found in the same order or proximity to each other as the search query "Shape of you".  Along with a few articles about the song "Shape of you", it pulls up articles about being in shape or what shape of your face says about you. 

When the `match query` is used to search for a phrase, it has high recall but low precision as it returns a lot of loosely related documents.

![image](https://user-images.githubusercontent.com/60980933/108571746-17058800-72ce-11eb-8b44-57df1cbb35d1.png)

#### Searching for phrases using the `match_phrase` query
Syntax: 
```
GET Enter_name_of_index_here/_search
{
  "query": {
    "match_phrase": {
      "Specify the field you want to search":{
        "query":"Enter search terms"
   }
  }
 }
}
```
Example: 
```
GET news_headlines/_search
{
  "query": {
    "match_phrase": {
      "headline":{
        "query":"Shape of You"
   }
  }
 }
}
```
When the `match_phrase` parameter is used, all hits must meet the following criteria:
1. the search terms "Shape", "of", and "you" must appear in the headline field.
2. the terms must appear in that order.
3. the terms must appear next to each other.

Expected response from Elasticsearch:

With `match_phrase` parameter, we get 3 hits returned. All 3 hits satisfy the criteria mentioned above. 

The `match_phrase` parameter yields higher precision but lower recall. 

![image](https://user-images.githubusercontent.com/60980933/108422566-f537d280-71f3-11eb-8340-2899b5fbc61e.png)

### Running a match query against multiple fields 
When designing a query, you do not always know the context of user's search. When a user searches for "Michelle Obama", the user could be searching for statements written by Michelle Obama or articles related to her. 

To accommodate these contexts, you can write a query designed search for terms in multiple fields.

This query runs a match query on each field and calculates a score for each field. Then it assigns the highest score among the fields to the document. 

Syntax:
```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query":"Enter search terms here",
      "fields": [
        "List the field you want to search over",
        "List the field you want to search over",
        "List the field you want to search over"
        ]
    }
  }
}
````
Example: Find the search terms "Michelle" and "Obama" in the headline, short_description, and author fields. 

```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query":"Michelle Obama",
      "fields": [
        "headline",
        "short_description",
        "authors"
        ]
    }
  }
}
```
Expected response from Elasticsearch:

We see 3044 hits that contain "Michelle Obama" in the headline or short_description or author field. While the multi_match query increased the recall, it decreased the precision of the hits. 

An article featuring Bernie Sanders as the main topic is pulled up as a top hit for a search regarding Michelle Obama. In this article, Michelle Obama is mentioned once in the short description. 

![image](https://user-images.githubusercontent.com/60980933/108615076-902fd880-73bd-11eb-97a8-9e600bb5952b.png)

#### Per-field boosting
Articles mentioning "Michelle Obama" in the headline are more likely to be related to our search than the articles that mention "Michelle Obama" once or twice in the short_description. 

To improve the precision of your search, you can designate one field to carry more weight more than others. 

This can be done by boosting the score of the field headline using the carat(^) symbol.

Syntax:
```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query":"Enter search terms",
      "fields": [
        "List field you want to boost^2",
        "List field you want to search over",
        "List field you want to search over"
        ],
  }
 }
}
```
Example:
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query":"Michelle Obama",
      "fields": [
        "headline^2",
        "short_description",
        "authors"
        ]
    }
  }
}
```
Expected response from Elasticsearch:

`Per-field boosting` yields same number of hits(5128). However, it changed the ranking of the hits. The hits ranked higher on the list have Michelle Obama in the boosted field, headline. 

These articles are more likely to be about Michelle Obama and we have improved the precision of our search!

![image](https://user-images.githubusercontent.com/60980933/108877609-a5268a80-75bc-11eb-8f61-ce1d1702a6e0.png)

#### What happens when you use the `multi_match` query to search for a phrase?

While searching for Michelle Obama, the user remembers that she is throwing a party for all of her friends this weekend. She searches for articles regarding party planning to get some ideas for it. 
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query":"party planning",
      "fields": [
        "headline^2",
        "short_description"
        ]
    }
  }
}
```
Response from Elasticsearch:

This query yields a lot of hits(2846). This happens because the terms "party" or "planning" are popular terms. With the `multi_match query`, a document is considered as a hit if any one of these search terms were found in any of these fields in any way shape or form. Because of this wide net, you will see irrelevant search results included among the hits. 

![image](https://user-images.githubusercontent.com/60980933/108582689-09fa9000-72f2-11eb-8e34-8f6cc4302254.png)

#### Improving precision with phrase type match
 
You can improve the precision of a `multi_match query` by adding a phrase type match in the query. 
The phrase type performs a match_phrase query on each field and calculates a score for each field. Then it assigns the best score to the document. 

Syntax:
```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query":"Enter search phrase",
      "fields": [
        "List field you want to boost^2",
        "List field you want to search over",
        "List field you want to search over"
        ],
        "type": "phrase"
    }
  }
}
```
Example: Look up the phrase "party planning" in the fields headline and short_description. Assign a higher score to documents containing the phrase "party planning" in the headline field. 
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query":"party planning",
      "fields": [
        "headline^2",
        "short_description"
        ],
        "type": "phrase"
    }
  }
}
```
Expected response from Elasticsearch:

The recall is much lower(6 vs 2846 hits) but every one of the hits have the phrase party planning in either the headline or short_description field or both. Among these, the hits that have the phrase party planning in the boosted field headline are ranked higher in the search results. 

![image](https://user-images.githubusercontent.com/60980933/108615437-4943e200-73c1-11eb-87ac-881b6a20d962.png)

## Combined Queries

There will be times when a user asks a multifaceted question that requires multiple queries to answer. 

For example, a user may want to find political articles about Michelle Obama published before the year 2016.  

This search is actually a combination of three queries:

1) Query articles that contain "Michelle Obama" in the headline field. 
2) Query Michelle Obama articles from the "POLITICS" category. 
3) Query Michelle Obama articles published before the year 2016

One of the ways you can combine these queries is through a `bool query`.

### Bool Query
The [bool query](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/query-dsl-bool-query.html#:~:text=Bool%20Queryedit,clause%20with%20a%20typed%20occurrence.) is a query that matches documents matching boolean combinations of other queries. 

There are four clauses to choose from: 

1. must
2. must_not
3. should 
4. filter

You can build combinations of one or more of these clauses.
Each clause can contain one or multiple queries that specify the criteria of each clause. 

These clauses are optional and can be mixed and matched to cater to your use case. The order in which they appear does not matter either! 

Syntax:
```
GET name_of_index/_search
{
  "query": {
    "bool": {
      "must": [
        {One or more queries can be specified here. A document MUST match all of these queries to be considered as a hit.}
      ],
      "must_not": [
        {A document must NOT match any of the queries specified here. It it does, it is excluded from the search results.}
      ],
      "should": [
        {A document does not have to match any queries specified here. However, it if it does match, this document is given a higher score.}
      ],
      "filter": [
        {These filters(queries) place documents in either yes or no category. Ones that fall into the yes category are included in the hits. }
      ]
    }
  }
}
```
#### A combination of query and aggregation request
A bool query can help you answer multi-faceted questions. 

To understand what type of questions we can ask about Michelle Obama, we need to first understand what articles have been written about her. 

One way to understand that is by searching for categories of articles that mention Michelle Obama. 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "query": {
    "Enter match or match_phrase here": { "Enter the name of the field": "Enter the value you are looking for" }
  },
  "aggregations": {
    "Name your aggregation here": {
      "Specify aggregation type here": {
        "field": "Name the field you want to aggregate here",
        "size": State how many buckets you want returned here
      }
    }
  }
}
```
Example: Query all data that has the phrase "Michelle Obama" in the headline. Then perform aggregations on the queried data and extract all categories that exist in the queried data. 
```
GET news_headlines/_search
{
  "query": {
    "match_phrase": { 
      "headline": "Michelle Obama"
   }
  },
  "aggregations": {
    "category_mentions": {
      "terms": {
       "field": "category",
       "size": 100
   }
  }
 }
}
```
Expected reponse from Elasticsearch:

When you minimize hits field(line 10), you will see an aggregations report called category_mentions. This report displays an array of all the categories that exist in the queried data and the number of articles that have been written about each category. 

We see that Michelle Obama has been written about diverse topics such as politics, black voices, parenting, taste, and even weddings! 

![image](https://user-images.githubusercontent.com/60980933/108541130-5668b000-729f-11eb-80aa-8e37b6dc016c.png)

#### The must clause
The `must clause` defines all the queries(criteria) a document MUST match to be returned as hits. These criteria are expressed in the form of one or multiple queries. All queries in the must clause must be satisfied for a document to be returned as a hit. As a result, having more queries in the `must clause` will increase the precision of your query. 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "Enter match or match_phrase here": {
          "Enter the name of the field": "Enter the value you are looking for" 
         }
        },
        {
          "Enter match or match_phrase here": {
            "Enter the name of the field": "Enter the value you are looking for" 
          }
        }
      ]
    }
  }
}
```

Example:
```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "match_phrase": {
          "headline": "Michelle Obama"
         }
        },
        {
          "match": {
            "category": "POLITICS"
          }
        }
      ]
    }
  }
}
```
Expected response from Elasticsearch: 

You will get 45 hits. All documents will contain "Michelle Obama" in the headline field and "POLITICS" in the category field. 
![image](https://user-images.githubusercontent.com/60980933/108631596-cbff8800-7427-11eb-9ac0-2f172075f9ed.png)

#### The must_not clause
The `must_not` clause defines queries(criteria) a document MUST NOT match to be included in the search results. 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "Enter match or match_phrase here": {
          "Enter the name of the field": "Enter the value you are looking for" 
         }
        },
       "must_not":[
         {
          "Enter match or match_phrase here": {
            "Enter the name of the field": "Enter the value you are looking for"
          }
        }
      ]
    }
  }
}
```
Example: All hits `must` contain Michelle Obama in the headline field. Documents `must_not` contain the term weddings in the category field. 

```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "must": {
        "match_phrase": {
          "headline": "Michelle Obama"
         }
        },
       "must_not":[
         {
          "match": {
            "category": "WEDDINGS"
          }
        }
      ]
    }
  }
}
```
Expected response from Elasticsearch:

This query increases the recall(203 hits). This query pulls up all hits that contain "Michelle Obama" in the headline field. Among the hits, Elasticsearch excludes all documents that has the term "WEDDINGS" in the category field.

![image](https://user-images.githubusercontent.com/60980933/108631753-99a25a80-7428-11eb-819a-c284e0ebb1b1.png)

#### The should clause
The `should clause` adds "nice to have" queries(criteria). The documents do not need to match the "nice to have" queries to be considered as hits. However, the ones that do will be given a higher score so it shows up higher in the search results. 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "Enter match or match_phrase here: {
          "Enter the name of the field": "Enter the value you are looking for" 
         }
        },
       "should":[
         {
          "Enter match or match_phrase here": {
            "Enter the name of the field": "Enter the value you are looking for"
          }
        }
      ]
    }
  }
```

Example: During the Black History Month, it is possible that the user is looking up Michelle Obama in the context of "BLACK VOICES" category rather than in the context of "WEDDINGS", "TASTE", or "STYLE" categories. 

All hits MUST contain Michelle Obama in the headline field. Having the phrase "BLACK VOICES" in the category is not required. However, if a document contains the phrase "BLACK VOICES" in the category field, this document will be given a higher score and shown higher in the search results.

```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "match_phrase": {
          "headline": "Michelle Obama"
          }
         }
        ],
       "should":[
         {
          "match_phrase": {
            "category": "BLACK VOICES"
          }
        }
      ]
    }
  }
}
```
Expected response: 

We should still get same number of hits(207) as the should clause does not add or exclude more hits. However, you will notice that the ranking of the documents has been changed. The documents with "BLACK VOICES" in the category field are now presented towards the top of the search results. 

![image](https://user-images.githubusercontent.com/60980933/108632370-be4c0180-742b-11eb-8c03-75c7809b54b8.png)

#### The filter clause
The `filter clause` contains filter queries that place documents in either "yes" or "no" category. 

For example, let's say you are looking for an article written in certain time range. Some documents will fall within this range(yes) or do not fall within this range(no). 

The `filter clause` only includes documents that fall in the yes category. 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "Enter match or match_phrase here": {
          "Enter the name of the field": "Enter the value you are looking for" 
         }
        }
        ],
       "filter":{
          "range":{
             "date": {
               "gte": "Enter lowest value of the range here",
               "lte": "Enter highest value of the range here"
          }
        }
      }
    }
  }
}
```
Example: All hits must include the phrase "Michelle Obama" in the headline. Among these hits, exclude any documents that have not published between the date range "2014-03-25" and "2016-03-25".
```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "must": [
        {
        "match_phrase": {
          "headline": "Michelle Obama"
          }
         }
        ],
       "filter":{
          "range":{
             "date": {
               "gte": "2014-03-25",
               "lte": "2016-03-25"
          }
        }
      }
    }
  }
}
```
Expected response from Elasticsearch: 

You will see 33 hits returned. All hits have been published between the date range we specified under the filter clause.  
![image](https://user-images.githubusercontent.com/60980933/108633174-15ec6c00-7430-11eb-9153-05d849673f3a.png)

#### Fine-tuning the relevance of bool queries

There are many ways you can fine-tune the relevance of bool queries.
One of the ways is to add multiple queries under the `should clause`. 

**Adding multiple queries under the `should clause`**
This approach ensures that you maintain a high recall but also offers a way to present more precise search results towards the top.

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "query": {
    "bool": {
      "must": [
        {"Enter match or match_phrase here": {"Enter the name of the field": "Enter the value you are looking for"}}
        ],
       "should":[
          {"Enter match or match_phrase here": {"Enter the name of the field": "Enter the value you are looking for"}},
          {"Enter match or match_phrase here": {"Enter the name of the field": "Enter the value you are looking for"}},
          {"Enter match or match_phrase here": {"Enter the name of the field": "Enter the value you are looking for"}}
      ]
    }
  }
}
```
Example: Let's say you want to run a search for articles with the phrase "Michelle Obama" in the headline field. But you want to favor articles that mention her biography "Becoming", and terms like "women" and "empower". 

To do this, you can add multiple queries in the `should clause`. 

This will cast a wide net because none of the queries in the `should clause` need to match. However, the ones that match the queries under the `should clause` will be given a higher score.  

This approach allows you to maintain a high recall but also gives you a way to customize the precision of top hits. 

Example:
```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "must": [
        {"match_phrase": {"headline": "Michelle Obama"}}
        ],
       "should":[
          {"match": {"headline": "Becoming"}},
          {"match": {"headline": "women"}},
          {"match": {"headline": "empower"}}
      ]
    }
  }
}
```
Expected response from Elasticsearch:

Adding many queries under the  `should clause` did not reduce the number of hits(207). However, it favored documents that match the queries in the `should` clause, improving the precision of top search results. 

![image](https://user-images.githubusercontent.com/60980933/108548611-51a8f980-72a9-11eb-8310-0fe14286e437.png)

