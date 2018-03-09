# Data Wrangling Steps for Capstone 1: 
_Amazon Rating Predictor_

## Book review data description
Consumer reviews and ratings. I have a dataset of over 22 million book reviews from Amazon.com from May 1996 - July 2014. These reviews are made available by Julian McAuley UCSD professor of Computer Science (McAuley et al. 2015; He & McAuley 2016). For this project, I will utilize a subset of all book reviews within two specific categories to train and test the algorithm. 
  * J. McAuley’s main page: http://cseweb.ucsd.edu/~jmcauley/
  * Amazon Review Data links: http://jmcauley.ucsd.edu/data/amazon/  Data files with all reviews are only available from Julian McAuley by request.
  * Metadata file from J. McAuley, with permission.
  * Count of reviews_Books records: 22507155
  * Count of 5.0 rated reviews: 13886788
  * Count of MetaData records: 9430088
  * For books with 10-digit International Standard Book Number (ISBN), the ASIN and the ISBN are the same.
  * See McAuley et al. 2015, He & McAuley 2016. Full citations below.

## Steps to access book review data
The book reviews json file is almost 20 GB. Therefore, I cannot open it with Jupyter Notebook. Instead I installed MongoDB and Studio 3T to access the json as a database file. Then, in Jupyter Notebook with PyMongo I can use aggregation functions to match the review documents I want to use (within a genre) for the Machine Learning algorithms.

## Additional data to augment review data
Because the book review data includes ASIN codes, but no title or genre information we need additional book data in order to identify the review documents in the large review data base that match the genre of interest for the Machine Learning, Natural Language Processing goal of the Capstone.

1. Use Google Books API to query for Invertebrate Biology textbooks. Query with API key masked: https://www.googleapis.com/books/v1/volumes?[query here]&maxResults=40&startIndex=0&printType=books&subject:textbook&key=xxxxxx

* I ran the query 8 times (320 records) with the search terms: q=science+biology+invertebrate+nonfiction

* I ran the query ten times (400 records) with the search terms: q=science+biology+nonfiction

2. The results of this query are nested JSON with nested objects and arrays. To access the key:value pairs of interest I coded my API query in Jupyter Notebook with 'requests.get' and I have used 'json_normalize' from pandas.io.json to flatten the nested json from the API. Useful fields are: 'title', 'author', 'description', and 'ISBN_10'. In addition, I access the nested array containing ISBN_10 I used the apply() function to access each line:

```python
# 'normalize' response json into a flat table
df3 = pd.io.json.json_normalize(data =json_data3['items'])
# use apply() to process each line and get ISBN_10
df3['volumeInfo.industryIdentifiers']
df3['id_isbn10'] = df3.apply(lambda x:[a['identifier'] for a in x['volumeInfo.industryIdentifiers'] if 'ISBN_10' in a.values()], axis=1)
               
df3['id_isbn10']=df3['id_isbn10'].apply(lambda x:x[0] if len(x) > 0 else 'None')
```

3. In Jupyter Notebook I queried the Google Books API 18 times, with 40 results per query. By normalizing the JSON object I accessed the nested fields of interest and compiled a DataFrame of 720 book titles & ISBN codes for my Biology Textbook Genre Machine Learning Model.

4. After I flattened and accessed the fields from the Google Books json requests I: 
   * kept only the columns of interest [title, subtitle, descrption, ISBN_10_]
   * concatenated the 18 results files
   * deleted rows with no ISBN code for a total of 607 unique books
   * renamed columns, and
   * pickled the cleaned up DataFrame to use it next to subset the main review collection.

## Using ISBN genre list to subset book review data set
One challenge of my Capstone data wrangling was to limit the huge number of book review data and subset to a set of reviews within a specific genre. The original book review data is already cleaned and 'de-duplicated', so after filtering the MongoDB collection of reviews to keep only one genre (Biology Textbooks)  the data are ready for the the next step of machine learning.

```python
# reset index of isbn dataframe with 'isbn10' and turn just 'isbn10' into a list
ans = isbn_bio.reset_index()['isbn10']
out = ans.values.tolist()

# use pymongo's find() function to search the 'asin' field in the collection with the contents of the 'out' list
bio_revs = DataFrame(list(db.reviews_Books.find({'asin':{"$in": out
       }})))
```
## Working Genre Review Collection
After completing the data wrangling to aggregate a subsetted collection of Amazon book reviews within the specified genre I have a DataFrame of 1663 reviews for 112 books. I can increase the number of reviews by running additional Google Books API queries.

### Sources:
Ruining He and Julian McAuley. 2016. Ups and Downs: Modeling the Visual Evolution of Fashion Trends with One-Class Collaborative Filtering. In Proceedings of the 25th International Conference on World Wide Web (WWW '16). International World Wide Web Conferences Steering Committee, Republic and Canton of Geneva, Switzerland, 507-517. DOI: https://doi.org/10.1145/2872427.2883037
 
Julian McAuley, Christopher Targett, Qinfeng Shi, and Anton van den Hengel. 2015. Image-Based Recommendations on Styles and Substitutes. In Proceedings of the 38th International ACM SIGIR Conference on Research and Development in Information Retrieval (SIGIR '15). ACM, New York, NY, USA, 43-52. DOI: http://dx.doi.org/10.1145/2766462.2767755
