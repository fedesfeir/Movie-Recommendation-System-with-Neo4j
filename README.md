# Movie-Recommendation-System-with-Neo4j
## A Graph-Based Hybrid Recommender Using the MovieLens Dataset
### Overview
This project implements a movie recommendation system built on the MovieLens dataset and powered by the Neo4j graph database.
Two main recommendation techniques are developed and compared:
- Content-Based Filtering → uses movie genres and Jaccard similarity
- Collaborative Filtering (User–User) → uses cosine similarity between users to generate personalized rating predictions
The goal is to evaluate their performance and build a robust, interpretable recommendation pipeline.

##Dataset
The system uses the MovieLens dataset containing:
movies.csv
ratings.csv

These are imported into Neo4j and mapped into the following graph schema:

Nodes

:User

:Movie

:Genre

Relationships

(:User)-[:RATED {rating}]->(:Movie)

(:Movie)-[:HAS_GENRE]->(:Genre)
