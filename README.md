# 27M-movieLens-dataset-processing
Processed movieLens dataset with 27M ratings. Scrap poster_url from IMDB for movies; Normalized ratings with decoupling normalization.

## Original dataset
[https://grouplens.org/datasets/movielens/latest/](https://grouplens.org/datasets/movielens/latest/)
* Number of raters: 283228
* Number of movies: 58098
* Number of ratings: 27753444 (values from 0.5 to 5 at step of 0.5)

## Processed movies dataset

### Scrap posters from [IMDB](https://www.imdb.com/)
* Scrap imdb_id, imdb_url, and poster_url for movies. 

* Yields 53806 movies from 58098 movies in original dataset. (Movies without available infos are not included in processed dataset)

### Normalize ratings with Decoupling Normalization method

* What is Decoupling Normalization?

  * Decoupling Normalization evaluates a rating r with respect to other ratings made by the same rater, mapping its value to range(0, 1)
  
    normalized(r) = percentage( all ratings by the same rater < r ) + percentage( all ratings by the same rater = r ) / 2
  
  * In comparison, Gaussian Normalization is to normalize ratings of a rater to have mean of 0 and standard deviation of 1.

* Why choosing it against Gaussian Normalization?

  * The paper [A Study of Methods for Normalizing User Ratings in Collaborative Filtering"](https://www.cs.purdue.edu/homes/lsi/sigir04-cf-norm.pdf) compares these two normalization methods for single-criteria collaborative filtering on a small dataset.
  
    This paper [IMPROVING ACCURACY OF MULTI-CRITERIA COLLABORATIVE FILTERING BY NORMALIZING USER RATINGS](https://pdfs.semanticscholar.org/0a38/aa813f16540ba2eaa3eda3a08f7c3814e079.pdf) compares these two normalization methods for multi-criteria collaborative filtering on a larger dataset.
    
    Both concludes that Decoupling Normalization yields better results.

  * When comparing normalized ratings of example users in this specific dataset, the basic observation is that Decoupling Normalization differentiates ratings in middle-high range (3, 4) better, which is where most ratings reside. See [MovieLens-Exploratory-Data-Analysis.ipynb](./MovieLens-Exploratory-Data-Analysis.ipynb) for more info.
