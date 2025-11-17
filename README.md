\documentclass[12pt,a4paper]{article}

\usepackage[utf8]{inputenc}
\usepackage{hyperref}
\usepackage{geometry}
\usepackage{setspace}
\usepackage{titlesec}
\usepackage{amsmath}
\usepackage{booktabs}

\geometry{margin=1in}
\setstretch{1.2}

\title{\textbf{README}\\Movie Recommendation System using Neo4j}
\author{Paola Maria Lepore \\ Federica Sfeir}
\date{}

\begin{document}

\maketitle

\section*{Overview}
This project implements a movie recommendation system using the MovieLens dataset and the Neo4j graph database.  
Two recommendation approaches are developed and compared:

\begin{itemize}
    \item \textbf{Content-Based Filtering}: based on movie genres and Jaccard similarity.
    \item \textbf{Collaborative Filtering (User--User)}: based on cosine similarity between users.
\end{itemize}

The objective is to evaluate the performance of both methods and produce accurate, interpretable recommendations.

\section*{Dataset}
The system uses the MovieLens dataset:
\begin{itemize}
    \item \texttt{movies.csv}
    \item \texttt{ratings.csv}
\end{itemize}

The data are imported into Neo4j with the following graph schema:

\subsection*{Nodes}
\begin{itemize}
    \item User
    \item Movie
    \item Genre
\end{itemize}

\subsection*{Relationships}
\begin{itemize}
    \item \texttt{(User)-[:RATED\{rating\}]->(Movie)}
    \item \texttt{(Movie)-[:HAS\_GENRE]->(Genre)}
\end{itemize}

\section*{Main Features}

\subsection*{1. Exploratory Data Analysis (EDA)}
\begin{itemize}
    \item User, movie, and genre statistics
    \item Most rated movies
    \item Most active users
    \item Highest-rated movies (filtered by number of ratings)
    \item Genre distribution and sparsity metrics
\end{itemize}

\subsection*{2. Content-Based Filtering}
\begin{itemize}
    \item Jaccard similarity between movie genre sets
    \item Weighted average prediction based on similarity
    \item Fallback mechanism: user avg $\rightarrow$ movie avg $\rightarrow$ global avg
\end{itemize}

\subsection*{Performance}
\begin{itemize}
    \item RMSE = 1.41
    \item MAE = 1.21
\end{itemize}

\subsection*{3. Collaborative Filtering (User--User)}
\begin{itemize}
    \item Cosine similarity between users
    \item Uses top 30 most similar users
    \item Weighted prediction using deviations from mean ratings
    \item Confidence score for recommendation reliability
\end{itemize}

\subsection*{Performance}
\begin{itemize}
    \item RMSE = 0.747
    \item MAE = 0.582
\end{itemize}

\section*{Validation with a New User}
A synthetic user (User 672) was created with 15 initial ratings to validate both models.

\begin{itemize}
    \item Content-Based: stable but homogeneous predictions due to sparse profile.
    \item Collaborative Filtering: diverse recommendations with high confidence.
\end{itemize}

\section*{Graph Architecture}

\[
\texttt{(User) -[:RATED\{rating\}]-> (Movie) -[:HAS\_GENRE]-> (Genre)}
\]

\section*{Setup and Installation}

\subsection*{Requirements}
\begin{itemize}
    \item Neo4j Desktop / Neo4j Aura
    \item MovieLens dataset
    \item Python 3.9+ (optional)
\end{itemize}

\subsection*{Neo4j Data Import}

\subsubsection*{Create Constraints}
\begin{verbatim}
CREATE CONSTRAINT FOR (m:Movie) REQUIRE m.id IS UNIQUE;
CREATE CONSTRAINT FOR (u:User) REQUIRE u.id IS UNIQUE;
\end{verbatim}

\subsubsection*{Import Movies}
\begin{verbatim}
LOAD CSV WITH HEADERS FROM 'file:///movies.csv' AS line
WITH line, SPLIT(line.genres, '|') AS genres
CREATE (m:Movie {id: toInteger(line.movieId), title: line.title})
UNWIND genres AS g
MERGE (gen:Genre {name: toUpper(g)})
CREATE (m)-[:HAS_GENRE]->(gen);
\end{verbatim}

\subsubsection*{Import Ratings}
\begin{verbatim}
LOAD CSV WITH HEADERS FROM 'file:///ratings.csv' AS line
MATCH (m:Movie {id: toInteger(line.movieId)})
MERGE (u:User {id: toInteger(line.userId)})
CREATE (u)-[:RATED {rating: toFloat(line.rating)}]->(m);
\end{verbatim}

\section*{Recommendation Methods}

\subsection*{Content-Based}
\begin{itemize}
    \item Jaccard similarity on genres
    \item Squared similarity used for ranking recommendations
    \item Best for explicit preference matching
\end{itemize}

\subsection*{Collaborative Filtering}
\begin{itemize}
    \item Cosine similarity
    \item Weighted deviation from user averages
    \item Confidence score for robustness
\end{itemize}

\section*{Performance Summary}

\begin{center}
\begin{tabular}{|c|c|c|}
\hline
\textbf{Model} & \textbf{RMSE} & \textbf{MAE} \\
\hline
Content-Based & 1.41 & 1.21 \\
Collaborative Filtering & 0.747 & 0.582 \\
\hline
\end{tabular}
\end{center}

\section*{Full Report}
All technical details, analysis, and visualizations are available in the included \texttt{Report.pdf}.

\section*{Authors}
\begin{itemize}
    \item Paola Maria Lepore
    \item Federica Sfeir
\end{itemize}

\section*{License}
MIT License (or another license if preferred).

\end{document}
