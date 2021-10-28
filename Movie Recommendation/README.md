## Data

The data I used comes from MovieLens, a movie recommendation service. It contains 20,000,263 movie ratings from 138,493 users across 27,278 movies.
‘movies.csv’ contains the movieIds (a unique identifier), title, and genres of every movie.
‘ratings.csv’ contains ratings of users on the movies in a tidy format. Each row has a userId, movieId, and rating of that user of that movie. The file also contains a timestamp column of when the rating was created, which we will not use for this analysis. How might you incorporate this data as well?
‘genome-tags.csv’ contains descriptions for 1,128 movie properties (atmospheric, thought-provoking, realistic, etc.) called genomes and their corresponding unique identifier tagId number.
‘MovieGenome.csv’ contains data about how strongly movies exhibit each genome. The genome score was computed using a machine learning algorithm on user-contributed content including tags, ratings, and textual reviews. The structure is a dense matrix, meaning every movie in the genome has a value for every one of the 1,128 tags in the genome. This has the hilarious impact of Pixar’s “Toy Story” being 1.9% about vampire-human love, 2% neo-nazis, and 1.8% Jane Austen.


## Contents of the folder

- **movies.rmd:** R code
- **movies.md:** Markdown report having the code as well as the analysis
- **datasets:** Data sets used in the project
- **movies_files:** Supplementary graphs for the markdown


