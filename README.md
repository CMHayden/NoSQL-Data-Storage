# Assignment One - NoSQL Data Storage

## Group 10
**Ridwan Mukhtar** *RM109 - H00247135*

**Mark Schmieg** *MS147 - H00338262*

**Callum Hayden** *CMH1 - H00223708*

## Table of Contents

* [Contributions](https://github.com/CMHayden/NoSQL-Data-Storage#contributions)
    * [Ridwan Mukhtar](https://github.com/CMHayden/NoSQL-Data-Storage#ridwan-mukhtar)
    * [Mark Schmieg](https://github.com/CMHayden/NoSQL-Data-Storage#mark-schmieg)
    * [Callum Hayden](https://github.com/CMHayden/NoSQL-Data-Storage#callum-hayden)
* [Task 1](https://github.com/CMHayden/NoSQL-Data-Storage#task-1)
* [Task 2](https://github.com/CMHayden/NoSQL-Data-Storage#task-2)
* [Task 3](https://github.com/CMHayden/NoSQL-Data-Storage#task-3)
* [Conclusion](https://github.com/CMHayden/NoSQL-Data-Storage#conclusion)
* [Appendix](https://github.com/CMHayden/NoSQL-Data-Storage#appendix)
    * [Task 1](https://github.com/CMHayden/NoSQL-Data-Storage#task-1-1)
    * [Task 2](https://github.com/CMHayden/NoSQL-Data-Storage#task-2-1)
    * [Task 3](https://github.com/CMHayden/NoSQL-Data-Storage#task-3-1)

## Contributions

In this section we will discuss a summary of the contributions of each group member, and any issues which arose while collaborating.

### Ridwan Mukhtar

### Mark Schmieg

### Callum Hayden

## Task 1

*Familiarise yourself with the IMDB data subset and provide an overview of the entities and how they are linked (e.g. Entity Relationship Diagram).*

*Details of entities, links, and other information provided gets us the full marks (4-5)*

IMDB, also known as the Internet Movie Database, is an online database which stores data related to films, tv shows, video games, etc. In this coursework, we are focusing on a subset of the IMDB dataset which only includes information relating to films. The dataset was provided to us as part of the coursework and includes the following files:

* **actors.csv**
This file stores information relating to actors. It stores an id to identify them, a name, and a gender.

* **directors.csv**
This file stores information relating to directors. It stores an id to identify them, a name, a rat...

* **movies.csv**
This file stores information relating to films. It stores an id to identify them, the title of the movie, and the year it was released.

* **ratings.csv**
This file stores ratings for different movies. It stores a movie id to state which movie the rating is for, along with a rank out of ten, the number of votes the movie has received, and the distribution of votes/rank.

* **runningtimes.csv**
This file stores the length of different movies. It stores a movie id to link to a movie, a time and a time1 where they may be the same, but are used to distinguish different running times between versions, and an option version of the movie such as DVD or extended cut.

* **writers.csv**
This file stores information about the writers of films. It stores an ID along with a name for each writer.

* **moviestoactors.csv**
This file stores information which relates a movie to an actor. It stores a movie id and an actor id to match them with the movies and actors files, along with information about the role being played by the actor in the given movie, such as the character the actor played.

* **moviestodirectors.csv**
This file stores information which relates a movie to a director. It stores a movie id and a director id to match them with the movies and directors files. It also includes the genre of the movie.

* **moviestowriters.csv**
This file stores information which relates a movie to a writer. It stores a movie id and an writer id to match them with the movies and writers files. It also includes information about the written format of the movie, such as if it was a screenplay, if a writer was uncredited, or if it was based on a novel by the writer amongst other options.

![UML Diagram](https://raw.githubusercontent.com/CMHayden/NoSQL-Data-Storage/master/images/UMLDiagram.png?token=AFNT2CCQ24QNCKFDKXN52226IWG5E)

## Task 2

*Load the data provided into Neo4J giving the details in your report on any data cleaning/transformations/checks/loading commands used. These should be detailed enough for someone else to implement given they know how to use Neo4J.*

*Full instructions on loading data with additional steps taken after loading dataset gets us full marks (8-10)*

After designing the database, we worked on creating the database. The first issue we encountered was with the formatting of the CSV file. We attempted to load data with this query:

```SQL
LOAD CSV WITH HEADERS FROM "file:///directors.csv" AS row FIELD RETURN row
```

Unfortunetly, this lead to a syntax error as neo4j did not understand how to sperate the data. This would have worked if each row of data was correctly formatted into columns, however, it was not.

We found two ways we could fix this issue. This first was to change the format of the CSV file to ensure all data was formatted correctly and as expected. The second was to change the query to include the terminator parameter, using ";" as the terminator. 

The solution we chose was to use the terminator so as not to edit the data as to the dataset only being a subset of the full IMDB dataset. If we chose to edit the data, then when adding the full database it may not work as expected.

The commands used to load the data can be found in the appendix. All of these commands should be ran to load all of the data files into the database.

Once the data files are loaded in, the next step is to create the graph itself. To do this we began by creating the nodes. The queries used can be found in the appendix, and the nodes created are the actors node, the directors node, the writers node and the movies node. We then created indexes for these nodes by using the code found in the appendix.

Once we had our nodes and their indexes, we needed a way to link them. To do this we created the relationships between the nodes, for example, the link between movies and writers. This link states that movies were written by a writer, and gives us a link between the nodes with which we can carry out queries involving both nodes. Some of these relationships such as that mentioned above links two nodes together, however, some create a new node such as the link between movies and rating, which creates a new rating node. The code for this can be found in the appendix.

We then tested the database by using some simple commands such as "call db.schema()" to see how the graph looked. The output to this query looks like so:

![Screenshot of output of call db.schema() command](https://raw.githubusercontent.com/CMHayden/NoSQL-Data-Storage/master/images/screenshotOfGraph.png?token=AFNT2CDPDJVD7IH2WXSWXMK6JQD7O)

And we also ran commands such as:

```SQL
MATCH(n:actors)
RETURN n
LIMIT 100
```

For each of our nodes. This returns the first 100 actors stored inside the actors node and is something we recommend to ensure the data is loaded as expected. You can remove the limit, however, the query may take a long time to run. Sample output should look similar to this:

![Output for 100 actors query](https://raw.githubusercontent.com/CMHayden/NoSQL-Data-Storage/master/images/100Actors.png?token=AFNT2CCL7DHXDSO6ILXKDZC6JQFD6)

## Task 3

*For 2 marks you need to provide Cypher code and correct answer; correct answer without any cypher code will score 0; Wrong answer but Cypher code demonstrating correct idea may score 1 mark. Max is 30 marks*

1.  How many female actos are listed in the dataset supplied?

    32896


2.  How many male actors are listed in the dataset supplied?

    65794


3.  Write a CYPHER query that shows the number of female actors and the number of male actors as a single query.

    "M" 65794

    "F" 32896


4.  List the movie titles and number of directors involved for movies with more than six directors.

    "Fantasia (1940)"	        11

    "Fantasia/2000 (1999)"	    8

    "Bambi (1942)"	            7

    "Dumbo (1941)"	            7

    "Duel in the Sun (1946)"	7

    "Pinocchio (1940)"	        7


5.  Number of movies with a running time of less than 10 minutes.

    7


6.  The movie titles which star both 'Ewan McGregor' and 'Robert Carlyle' (i.e. both actors were in the same film)

    "Being Human (1994)"

    "Trainspotting (1996)"


7.  Number of movies directed by 'Spielberg'.

    14


8.  List the male/female actors that have worked together on more than ten films, include their names and number of films they've co-starred in.

9.  List the number of movies released per decade as listed here: 1960-1969, 1970-1979, 1980-1989, 1990-1999, 2000-2010.

10. How many movies have more female actors than male actors?

    324


11. Based ratings with ten thousand or more votes, what are the top three movie genres using the average rank per movie genre as the metric? (Higher value for rank is considered a better movie).

    7.625   "Western"

    7.6	    "Documentary"

    7.5	    "Film-Noir"


12. Show the shortest path between actors 'Ewan McGregor' and 'Mark Hamill' from the IMDB data subset. Include nodes and edges. 

    (Ewan McGregor)<- [ casts ]-(Star Wars: Episode I - The Phantom Menace) -[ casts ]-> (Daniels, Anthony (I)) <-[ casts ]- (Star Wars (1977)) -[ casts ]-> (Mark Hamill)

    ![Graph output for this query](https://raw.githubusercontent.com/CMHayden/NoSQL-Data-Storage/master/images/question%2012.png?token=AFNT2CCULVHEFVHJMR6HP426JQHYW)


13. List all actors (male/female) that have starred in ten or more different film genres (show names, and number of genres).

14. How many movies have an actor/actress that also directed the movie?

15. How many movies have been written and directed by an actor/actress that they didn't star in? *i.e. the person who wrote and directed the movie is a film star but didn't appear in the movie*

## Conclusion

In conclusion, we learnt how to truly collaborate. While it would have been easier to simply split up the queries into five each and get them done individually, we chose to attempt to collaborate. We used GitHub (private repository) to help with our collaboration and we attempted to all work on everything. The harder queries we sat together and attempted to figure them out between us which lead to a great experience of learning to be better team players. This also allowed us to discuss how we are doing queries, and which we believe would be the more efficient way of doing a query, along with all pitching in towards the design of the graph itself.

Apart from our soft skills, we all learnt massively about graph databases and enjoyed finding similarities between it with computing concepts we already know such as graph data structures, SQL and object-oriented programming. One of the biggest similarities found however was with JSON which helped a lot when thinking how write the complex queries.

We all got to learn and practice cypher syntax. From simple queries such as counting the amount of male actors, to more complex queries such as query 11 which matches multiple nodes and edges to get the required data.


## Appendix

### Task 1

### Task 2

```sql
--Loading data
LOAD CSV WITH HEADERS FROM "file:///actors.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///directors.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///movies.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///ratings.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///runningtimes.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///writers.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///moviestoactors.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///moviestodirectors.csv" AS row FIELDTERMINATOR ';' RETURN row
LOAD CSV WITH HEADERS FROM "file:///moviestowriters.csv" AS row FIELDTERMINATOR ';' RETURN row

--Create an actors node
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///actors.csv" AS row FIELDTERMINATOR ';'
CREATE (:actors {actorid: row.actorid, name: row.name, sex: row.sex});

--Create a directors node
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///directors.csv" AS row FIELDTERMINATOR ';'
CREATE (:directors {directorid: row.directorid, name: row.name, rate: toFloat(row.rate), gross: toFloat(row.gross), num: row.num});

--Create a movies node
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///movies.csv" AS row FIELDTERMINATOR ';'
CREATE (:movies {movieid: row.movieid, title: row.title, year: row.year});

--Create a writers node
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///writers.csv" AS row FIELDTERMINATOR ';'
CREATE (:writers {writerid: row.writerid, name: row.name});

--this creates a way to quick lookup on the nodes
CREATE INDEX ON :actors(actorid);
CREATE INDEX ON :directors(directorid);
CREATE INDEX ON :movies(movieid);
CREATE INDEX ON :writers(writerid);

--link between movies and actors
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///moviestoactors.csv" AS row FIELDTERMINATOR ';'
MATCH (actors:actors {actorid: row.actorid})
MATCH (movies:movies {movieid: row.movieid})
MERGE (movies)-[c:casts]->(actors)
ON CREATE SET c.as_character = row.as_character, c.leading = row.leading;

--link between movies and directors
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///moviestodirectors.csv" AS row FIELDTERMINATOR ';'
MATCH (directors:directors {directorid: row.directorid})
MATCH (movies:movies {movieid: row.movieid})
MERGE (movies)-[d:directed_by]->(directors)
ON CREATE SET d.genre = row.genre;

--link between movies and writers
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///moviestowriters.csv" AS row FIELDTERMINATOR ';'
MATCH (writers:writers {writerid: row.writerid})
MATCH (movies:movies {movieid: row.movieid})
MERGE (movies)-[w:written_by]->(writers)
ON CREATE SET w.addition = row.addition;

--create and link movies to ratings
USING PERIODIC COMMIT 
LOAD CSV WITH HEADERS FROM "file:///ratings.csv" AS row FIELDTERMINATOR ';'
MATCH (m:movies {movieid: row.movieid})
CREATE (r:ratings)
SET r+= row
CREATE (m)-[:has_rating]->(r)

--create and link movies to runtime
USING PERIODIC COMMIT 
LOAD CSV WITH HEADERS FROM "file:///runningtimes.csv" AS row FIELDTERMINATOR ';'
MATCH (m:movies {movieid: row.movieid})
CREATE (t:runtime)
SET t+= row
CREATE (m)-[:length]->(t)
```

### Task 3

```sql
--1: 
COUNT MALES
MATCH (a:actors)  
WHERE a.sex="M" 
RETURN COUNT(a) AS MaleCount

--2: 
COUNT FEMALES
MATCH (a:actors)  
WHERE a.sex="F" 
RETURN COUNT(a) AS FemaleCount

--x--x--x--x--x--x--x--x--x--x--x--x--x--x--x--x--x--

--6:
Movies with Ewan McGregor and Robert Carlyle
MATCH (m:movies),(o:actors {name:"McGregor, Ewan"}), (r:actors {name:"Carlyle, Robert (I)"})
WHERE (m)-[:casts]->(o) AND (m)-[:casts]->(r)
RETURN m.title AS movieTitles


--7:
Movies Directed by Spielberg
MATCH (m:movies)-[:directed_by]->(d:directors {name:"Spielberg, Steven"})
RETURN count(m) AS SpielbergMovies
//Answer is 14
```
