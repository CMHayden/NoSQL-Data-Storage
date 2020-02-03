# Assignment One - NoSQL Data Storage

## Group 10
**Ridwan Mukhtar** *TODO - TODO*

**Mark Schmieg** *TODO - TODO*

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
    * [Task 1]()
    * [Task 2]()
    * [Task 3]()

## Contributions

In this section we will discuss a summary of the contributions of each group member

### Ridwan Mukhtar

### Mark Schmieg

### Callum Hayden

## Task 1

*Familiarise yourself with the IMDB data subset and provide an overview of the entities and how they are linked (e.g. Entity Relationship Diagram).*

*Details of entities, links, and other information provided gets us the full marks (4-5)*

## Task 2

*Load the data provided into Neo4J giving the details in your report on any data cleaning/transformations/checks/loading commands used. These should be detailed enough for someone else to implement given they know how to use Neo4J.*

*Full instructions on loading data with additional steps taken after loading dataset gets us full marks (8-10)*

## Task 3

*For 2 marks you need to provide Cypher code and correct answer; correct answer without any cypher code will score 0; Wrong answer but Cypher code demonstrating correct idea may score 1 mark. Max is 30 marks*

1.  How many female actos are listed in the dataset supplied?

2.  How many male actors are listed in the dataset supplied?

3.  Write a CYPHER query that shows the number of female actors and the number of male actors as a single query.

4.  List the movie titles and number of directors involved for movies with more than six directors.

5.  Number of movies with a running time of less than 10 minutes.

6.  The movie titles which star both 'Ewan McGregor' and 'Robert Carlyle' (i.e. both actors were in the same film)

7.  Number of movies directed by 'Spielberg'.

8.  List the male/female actors that have worked together on more than ten films, include their names and number of films they've co-starred in.

9.  List the number of movies released per decade as listed here: 1960-1969, 1970-1979, 1980-1989, 1990-1999, 2000-2010.

10. How many movies have more female actors than male actors?

11. Based ratings with ten thousand or more votes, what are the top three movie genres using the average rank per movie genre as the metric? (Higher value for rank is considered a better movie).

12. Show the shortest path between actors 'Ewan McGregor' and 'Mark Hamill' from the IMDB data subset. Include nodes and edges. *Answer can be shown as an image or text description in form (a)-[ ]->(b)-[ ]-> (c)… So possibly best to do both?*

13. List all actors (male/female) that have starred in ten or more different film genres (show names, and number of genres).

14. How many movies have an actor/actress that also directed the movie?

15. How many movies have been written and directed by an actor/actress that they didn't star in? *i.e. the person who wrote and directed the movie is a film star but didn't appear in the movie*

## Conclusion

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