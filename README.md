# A-star-in-graph-database

This is real time implementation for applying a star algorithm in graph database [Neo4j]. <br/>

For visualisation we have used Neovis.js <br/>


# Getting Started

## Installation

Detailed installation guide for latest Neo4j can be found <a href="https://neo4j.com/docs/operations-manual/current/installation/">here</a>.

### Check

Check if the Neo4j is working by opening http://localhost:7474/browser<br/>

Enter your Username and password and you're good to go.

## Pre-requisites

### Important Jar Files

Download the Jar Files of APOC which already have a-star algorithm implemented, which are given in this repository.<br/>
Copy these JAR files to <b>&lt;neo4j-home&gt;/plugins</b>.

### Importing Dataset

Dataset can be found <a href="https://github.com/geoiq/acetate/tree/master/places">here</a>.<br/>

Dataset can be imported using this command in your Neo4j browser :

```
LOAD CSV WITH HEADERS FROM 
"https://raw.githubusercontent.com/geoiq/acetate/master/places/Europe-z4-z6.txt"
as row FIELDTERMINATOR "\t"
MERGE (city:City{name: row.name})
ON CREATE SET city.population = toINT(row.population), city.longitude = toFLOAT(row.longitude), city.latitude = toFLOAT(row.latitude) 
MERGE (country:Country{code: row.`country code`})
MERGE (city)-[:IS_IN]->(country)
```

You can change the continents using the places dataset. I've used Asia, Europe and Africa. <br/>

### Distance Calculation

*We calculated distance between cities and save it as a relationship property between them where the distance is less than 250km. This way we set a threshold to travel less than 250km per day on our trip and still wind up in one of the cities on the list every day.*

```
WITH 250 as distanceInKm
MATCH (c1:City),(c2:City)
WHERE id(c1) < id(c2)
WITH c1,c2,
distance(point({longitude:c1.longitude,latitude:c1.latitude}), 
         point({longitude:c2.longitude,latitude:c2.latitude})) as distance
WHERE distance < (distanceInKm * 1000) 
MERGE (c1)-[l:LINK]->(c2)
ON CREATE SET l.distance = distance
```
## Running the Project

Open the html file provided in the repository. Give the places => "from" and "to" <br/>
You'll get the shortest path between two cities with max. 250 km distance constraint. <br/>
The Nodes diameter increases with the increase in population of thge city and the edge's width increases with the increase in distance between the two cities, considering maximum distance to be 250 kms.



