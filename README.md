# Movie Recommendation System using Neo4j
## Hybrid Movie Recommender with the MovieLens Dataset
Comparison between Content-Based Filtering and Collaborative Filtering

## Repository Contents
- Report.pdf – full technical report of the project
- movies.csv – MovieLens movies dataset
- ratings.csv – MovieLens ratings dataset

Project code and Cypher queries for:
Data import into Neo4j
Content-Based Filtering with Jaccard similarity
Collaborative Filtering with cosine similarity
Evaluation procedures (RMSE, MAE)
New user rating simulation
Graph model schema
Final comparison of recommendation performance

## Project Objectives
The project investigates:
The use of Neo4j to model users, movies, ratings, and genres as a graph.
The implementation of two different recommendation strategies:
- Content-Based Filtering
- Collaborative Filtering (User–User)

The advantages and limitations of both methods.

A quantitative comparison in terms of:
- prediction accuracy (RMSE, MAE)
- stability of predictions
- ability to generalize to new users
- quality and variability of recommendations

(User) -[:RATED {rating}]-> (Movie) -[:HAS_GENRE]-> (Genre)

Nodes:
- User
- Movie
- Genre

Relationships:
- RATED (rating 0.5–5.0)
- HAS_GENRE

## Methods Implemented
### Content-Based Filtering
Based on the similarity between movies, computed using the Jaccard index on genres.
Main components:
- Movie–genre graph exploration
- Similarity matrix
- Weighted rating prediction
- Fallback mechanism (user mean → movie mean → global mean)

### Collaborative Filtering (User–User)
Based on cosine similarity between users’ common ratings.
Components:
- Similarity computation between users
- Top-k neighbors (k = 30)
- Weighted deviation rating prediction
- Confidence score for stability

## Performance Summary
| Model                   | RMSE      | MAE       |
| ----------------------- | --------- | --------- |
| Content-Based Filtering | 1.41      | 1.21      |
| Collaborative Filtering | **0.747** | **0.582** |
Collaborative Filtering clearly provides better accuracy and more diverse recommendations.

## New User Simulation
A fictitious user (ID 672) was added with 15 initial ratings.
Both models were tested:
- Content-Based → stable but homogeneous predictions
- Collaborative Filtering → more diverse, dynamic, and better aligned recommendations

## Load Data into Neo4j (Cypher)
#### Create Constraints
```cypher
CREATE CONSTRAINT FOR (m:Movie) REQUIRE m.id IS UNIQUE;
CREATE CONSTRAINT FOR (u:User) REQUIRE u.id IS UNIQUE;

#### Movies
```cypher
LOAD CSV WITH HEADERS FROM 'file:///movies.csv' AS line
WITH line, SPLIT(line.genres, '|') AS genres
CREATE (m:Movie {id: toInteger(line.movieId), title: line.title})
UNWIND genres AS g
MERGE (gen:Genre {name: toUpper(g)})
CREATE (m)-[:HAS_GENRE]->(gen);

#### Ratings
```cypher
LOAD CSV WITH HEADERS FROM 'file:///ratings.csv' AS line
MATCH (m:Movie {id: toInteger(line.movieId)})
MERGE (u:User {id: toInteger(line.userId)})
CREATE (u)-[:RATED {rating: toFloat(line.rating)}]->(m);

## Authors
Federica Sfeir
Paola Maria Lepore
