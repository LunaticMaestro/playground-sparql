# playground-sparql
How I tried to learn sparql


## Reference
https://cambridgesemantics.com/blog/semantic-university/learn-sparql/
https://cambridgesemantics.com/blog/semantic-university/learn-sparql/sparql-nuts-bolts/

## Code

**Execute in the public sparql UI: https://dbpedia.org/sparql/**

1. Basic graph pattern: Get all cities of texas

```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>
}
```

2. Basic graph pattern: Get Texas cities and their population
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>.
 ?city dbp:populationTotal ?popTotal.
}
```

3. Basic graph pattern translated in tutrle format
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
       dbp:populationTotal ?popTotal.
}
```

4. Example 2:
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
         dbp:populationTotal ?popTotal;
         dbp:populationMetro ?popMetro.
}
```

5. Left-outer join: Include all cities even if they lack the attribute popluationMetro.
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
        dbp:populationTotal ?popTotal.
 OPTIONAL{?city dbp:populationMetro ?popMetro.}
}
```

6. Order by clause `desc` or `asc`.
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
         dbp:populationTotal ?popTotal.
 OPTIONAL{?city dbp:populationMetro ?popMetro.}
}
ORDER BY desc(?popTotal)
```

7. Limit results: `(offset, limit]`
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
         dbp:populationTotal ?popTotal.
 OPTIONAL{?city dbp:populationMetro ?popMetro.}
}
ORDER BY desc(?popTotal)
LIMIT 10
OFFSET 1
```

8. Filter: Popluation > 50000
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
         dbp:populationTotal ?popTotal.
 OPTIONAL{?city dbp:populationMetro ?popMetro.}
 FILTER (?popTotal > 50000)
}
ORDER BY desc(?popTotal)
LIMIT 10
OFFSET 1
```

9. Human readable name and i8n. Alternate clause `lang(?name)="en"`
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
         dbp:populationTotal ?popTotal;
         rdfs:label ?name.
 OPTIONAL{?city dbp:populationMetro ?popMetro.}
 FILTER (?popTotal > 50000)
}
ORDER BY desc(?popTotal && langmatches(lang(?name), "EN"))
LIMIT 10
OFFSET 1
```

10. Getting only null values for `popMetro`
```sparql
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dbp: <http://dbpedia.org/ontology/>
SELECT * 
WHERE {
 ?city rdf:type <http://dbpedia.org/class/yago/WikicatCitiesInTexas>;
         dbp:populationTotal ?popTotal;
         rdfs:label ?name.
 OPTIONAL{?city dbp:populationMetro ?popMetro.}
 FILTER (?popTotal > 50000)
 FILTER(!bound(?popMetro))
}
ORDER BY desc(?popTotal && langmatches(lang(?name), "EN"))
LIMIT 10
OFFSET 1
```
