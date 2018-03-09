# Predicting reader’s book rating from their written reviews: A test using two distinct genres.
__Springboard Capstone 1 Project Proposal__

### Introduction:
When consumers consider purchasing a product, they often turn to reviews and ratings submitted by other customers to determine if the purchase is worthwhile. Conversely, retailers depend on honest and accurate reviews and ratings to ensure subsequent buyers can make informed purchases. On average customers read more than two Yelp reviews before deciding to use a business (Rudolph 2015). Further, a one-star increase in ratings on Yelp leads to a 5-9% increase in revenue for a business (Rudolph 2015). Like business ratings, product ratings and reviews also affect sales. Therefore, accurate and error-free reviews and ratings are extremely valuable to retailers. The sentiment captured in the text of a review should be reflected in the star rating. One-star ratings potentially have a big negative effect on sales, so retailers need tools to flag incongruous reviews and ratings that may indicate user error. Similarly, high ratings paired with scathing review-text may indicate errors or other issues with the product or review system. Can we predict ratings (5-star) based on review features? I propose to build a machine-learning model using Natural Language Processing on two distinct sub-genres of books reviewed on Amazon.com. I will use review text features to predict ratings and compare predictions to actual ratings.
 
### Problem Statement:
Both consumers and booksellers depend on book reviews and ratings to make informed decisions about purchases and to help with sales. Positive and negative ratings and reviews help buyers and sellers know what to spend money on and what products to avoid. Errors and inconsistencies in these assessments can directly affect sales and customer satisfaction. I propose to use features of consumer book review text to determine if reviews can predict ratings. Being able to predict ratings based on review features has multiple benefits: 1) catch errors by reviewers where they accidentally selected the wrong number of stars, 2) suggest ratings when reviewers do not provide a star rating along with their review, 3) flag confusing/incongruous review-rating pairs for revision (by reviewer) or so that they are not featured first in review lists, and potentially 4) identify and flag reviews and ratings that are ‘fake’ or jokes based on the text of the review.
 
### Client profile:
My preliminary clients are booksellers. From small brick-and-mortar bookstores to large online book retailers, booksellers depend on consumer reviews to 1) make decisions on what books to purchase for resale and 2) to promote book sales from their platform. The machine learning algorithm can be used by retailers internally or as part of their review platform used by consumers.
 
Ultimately, my machine-learning algorithm that predicts star ratings from review text features can be utilized for any product-category or business. I envision a review platform that facilitates consumer review writing. This platform could incorporate a text editor (like Grammarly) to help reviewers craft clear and effective reviews in addition to suggesting a star rating based on the specific rating system of a given retailer. Together these features will help reviewers communicate more clearly and select corresponding ratings more consistently.

### Data Sources
* Consumer reviews and ratings. I have a dataset of over 22 million book reviews from Amazon.com from May 1996 - July 2014. These reviews are made available by Julian McAuley UCSD professor of Computer Science (McAuley et al. 2015; He & McAuley 2016). For this project, I will utilize a subset of all book reviews within two specific categories (e.g., science fiction literature reviews and college science textbook reviews) to train and test the algorithm. See outline below for details.
  * J. McAuley’s main page: http://cseweb.ucsd.edu/~jmcauley/
  * Amazon Review Data links: http://jmcauley.ucsd.edu/data/amazon/  Data files with all reviews are only available from Julian McAuley by request.
  * Metadata file from J. McAuley, with permission.
  * Review and metadata files will be indexed by the Amazon Standard Identification Number (ASIN), a unique code which is the same as the 10-digit ISBN in the case of books
* ISBN data from http://isbndb.com/. The API allows 500 records free daily. I will match the ISBN with the ASIN index in the Amazon review data to increase the classification fields available for my analysis (e.g., genre, category, subject, etc.).

### Capstone Project Outline:
1. Subset one genre/sub-genre of reviews from recent publications, recently reviewed from using PyMongo in Jupyter Notebook with a MongoDB instance running. 

   1. First genre: Fiction, Sci-Fi.
   1. Use genre/subject ISBN list from isbndb.com to identify review documents in book review database.
1. With genre-specific data frame of book reviews in Jupyter Notebook begin Exploratory Data Analysis. EDA tasks include:

   1. Check publication dates
   1. Count number of titles
   1. Calculate number of reviews per title (mean)
   1. Assess: how many reviews, how many ratings, how many incomplete (only reviews or ratings)
   1. Calculate average number of words per review
   1. Graph distribution of ratings (62% of the 22 million Amazon product reviews in my database are 5-star reviews)
1. Create new features from reviews:

   1. Length features (character count, word count)
   1. Word features (average word length)
   1. Occurrences of “ALL CAPS” text features in reviews
   1. Occurrences of expletive text features in reviews
1. Machine Learning for Sentiment Analysis. Incorporate the following methods:

   1. Simple classification and simple regression
   1. Gauge sentiment from positive and negative word usage
   1. TF-IDF: Term Frequency-Inverse Document Frequency
   1. Predict star ratings based on review features.
1. Repeat the process with a second, distinct book review subset. 

   1. Second genre: college science textbooks. 
   1. What features are different? 
   1. How different is the ML algorithm?
 
### Deliverables:
The initial products will include code for the ratings prediction algorithm and slides presenting the results and the rating prediction tool for retailers to use for multiple purposes: 1) predict or suggest a star rating when a reviewer only enters a review, 2) identify or flag problematic review-rating pairs (where the rating doesn’t match review content due to user error or nefarious intent).

In addition, these algorithms could be developed further to be a tool for reviewers. As part of the reviewing platform, the algorithm could suggest star ratings as reviewers build their review based on the site’s specific star rating system. Reviewers could select a different rating, but this could help standardize the rating scale within a given site.
 
### Sources:
Rudolph, Stacey. (2015, July 25). The Impact of Online Reviews on Customers’ Buying Decisions [Infographic]. Retrieved from: https://www.business2community.com/infographics/ impact-online-reviews-customers-buying-decisions-infographic-01280945#etm1uliB3CDhGtdP.99
 
Ruining He and Julian McAuley. 2016. Ups and Downs: Modeling the Visual Evolution of Fashion Trends with One-Class Collaborative Filtering. In Proceedings of the 25th International Conference on World Wide Web (WWW '16). International World Wide Web Conferences Steering Committee, Republic and Canton of Geneva, Switzerland, 507-517. DOI: https://doi.org/10.1145/2872427.2883037
 
Julian McAuley, Christopher Targett, Qinfeng Shi, and Anton van den Hengel. 2015. Image-Based Recommendations on Styles and Substitutes. In Proceedings of the 38th International ACM SIGIR Conference on Research and Development in Information Retrieval (SIGIR '15). ACM, New York, NY, USA, 43-52. DOI: http://dx.doi.org/10.1145/2766462.2767755
