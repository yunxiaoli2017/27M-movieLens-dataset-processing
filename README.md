# 27M-movieLens-dataset-processing

Processed movieLens dataset with 27M ratings for use of Java movie recommendation engine in [movie-recommender](https://github.com/yunxiaoli2017/movie-recommender).

## Original dataset
[MovieLens Latest Datasets](https://grouplens.org/datasets/movielens/latest/)
* Number of raters: 283228
* Number of movies: 58098
* Number of ratings: 27753444 (values from 0.5 to 5 at step of 0.5)

## Processing movies dataset (Scrap posters from [IMDB](https://www.imdb.com/))

* Scrap imdb_id, imdb_url, and poster_url for movies. 

* Yields 53806 movies from 58098 movies in original dataset. (Movies without available infos are not included in processed dataset)

## Processing ratings dataset (Normalize ratings with Decoupling Normalization method)

* What is Decoupling Normalization?

  * Decoupling Normalization evaluates a rating r with respect to other ratings made by the same rater, mapping its value to range(0, 1)
  
    normalized(r) = percentage( all ratings by the same rater < r ) + percentage( all ratings by the same rater = r ) / 2
  
  * In comparison, Gaussian Normalization is to normalize ratings of a rater to have mean of 0 and standard deviation of 1.

* Why choosing it against Gaussian Normalization?

  * There're papers like
  
  [A Study of Methods for Normalizing User Ratings in Collaborative Filtering"](https://www.cs.purdue.edu/homes/lsi/sigir04-cf-norm.pdf) and 
  
  [IMPROVING ACCURACY OF MULTI-CRITERIA COLLABORATIVE FILTERING BY NORMALIZING USER RATINGS](https://pdfs.semanticscholar.org/0a38/aa813f16540ba2eaa3eda3a08f7c3814e079.pdf) 
  that compare these two normalization methods and conclude that Decoupling Normalization yields better results.

  * When comparing normalized ratings of example users in this specific dataset, the basic observation is that Decoupling Normalization differentiates ratings in middle-high range (3, 4) better, which is where most ratings reside. See [movieLens-data-processing.ipynb](./movieLens-data-processing.ipynb) for more info.
  
## Extract a popular movies dataset

* Motivation

  * In this web app, users are first prompted to rate some randomly picked movies from dataset. And recommendations will be based on that. The problem is, users won't find many movies they knew before. 
  
* Approach
  * Form a popular movies dataset with a subset of movies, all having more than 1,000 ratings.
  
## Extract a compact ratings dataset

* Motivation
  
  * The time cost to generate recommendations was quite high, mainly from the step of querying for ratings on a certain movie from 25 million rows. 
  * Creating index on movie_id column alone isn't enough, because some movies have more than 10,000 ratings and pulling that many rows often end up with a bitmap scan.
  * From another perspective, different numbers of ratings for movies present a bias on recommendation: the algorithm will lean towards finding more raters with similar tastes on those particular movies with huge number of ratings, and fewer raters on movies with fewer ratings.
  
* Approach

  * Raters with fewer ratings are harder to be utilized, especially when they mainly rate those over-represented movies. (chosen as movies with more than 5,000 ratings) Two million ratings associated with these raters and over-represented movies are removed.
  
  * Ratings on movies with more than 1,000 ratings get subsampled to 1,000 ~ 2,000 ratings only, leaving 4 million ratings in total. See [form-compact-ratings-and-popular-movies.ipynb](./form-compact-ratings-and-popular-movies.ipynb) for more info.
  
  * Note that the compact ratings dataset is only needed when identifying similar users. When generating top recommended movies there's no time cost issue to use full dataset.
  
