# Predicting ratings from book review text
First Capstone for Springboard Career Track

## Summary and Motivation
When consumers consider purchasing a product, they often turn to reviews and ratings submitted by other customers to determine if the purchase is worthwhile. Conversely, retailers depend on honest and accurate reviews and ratings to ensure subsequent buyers can make informed purchases. Like business ratings, product ratings and reviews also affect sales. Therefore, accurate and error-free reviews and ratings are extremely valuable to retailers. The sentiment captured in the text of a review should be reflected in the star rating. One-star ratings potentially have a big negative effect on sales, so retailers need tools to flag incongruous reviews and ratings that may indicate user error. Similarly, high ratings paired with scathing review text may indicate errors or other issues with the product or review system. Can we predict ratings whether ratings are high or low based on review features? I used Natural Language Processing methods and fit machine learning algorithms to training data to predict high and low reviews in testing data. With scikit-learn's feature selection tools I identify text features most associated with high and low ratings.

Both consumers and vendors depend on reviews and ratings to make informed decisions about purchases and to help with sales. Positive and negative ratings and reviews help buyers and sellers know what to spend money on and what products to avoid. Errors and inconsistencies in these assessments can directly affect sales and customer satisfaction. Here I use features of consumer book review text to determine if reviews can predict ratings. Being able to predict ratings based on review features has multiple benefits for potential clients: 1) catch errors by reviewers where they accidentally selected the wrong number of stars, 2) suggest ratings when reviewers do not provide a star rating along with their review, 3) flag confusing/incongruous review-rating pairs for revision (by reviewer) or so that they are not featured first in review lists, and potentially 4) identify and flag reviews and ratings that are ‘fake’ or jokes based on the text of the review.

My final classification model using LogisticRegression with cross-validation on bag-of-words and bigrams classifies many Amazon reviews with 5-star ratings correctly as 'high' rated and many Amazon book reviews associated with lower ratings as 'low' rated. This accuracy of classification is a great step towards building tools for product review writing and rating. 

## Index of Repository
| File or Folder Name               | Description                     |
| -----------------       |--------------------------------|
| Graphics                | Folder of Images for Notebooks  | 
| Capstone 1 Slide Deck.pdf  | Review prediction slides     |
| Capstone Final Report Rating Predictor.ipynb | Final Project Report |
| DataWranglingReport.md  | Documented wrangling steps      |
| Inferential Statistics Amazon Book Revs.ipynb | Report of Stats Analyses |
| Inferential Statistics Amazon Book Working Code.ipynb | Stats Analyses Code |
| Machine Learning Amazon Reviews Working Code.ipynb |  All ML Code for Rating Predictor |
| Milestone Report for Rating Predictor.ipynb | Report of Capstone Progress |
| ProjectProposal.md      | Initial Proposal of Capstone    |
| README.md               | Summary and Overview of Capstone|
| WordClouds Amazon Review.ipynb | Code for Word Cloud Graphics|
|||