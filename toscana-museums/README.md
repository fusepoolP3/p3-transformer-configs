Transformer configuration for Tuscany Museums dataset
---------------------------------------------

## Input Data

As input data we use a CSV file provided by the Tuscany region, namely [musei.csv](musei.csv).
> http://mappe.regione.toscana.it/db-webgis/musei/example_postgis.jsp?format=csv

This dataset aims at uncovering cultural locations in the region, mainly museums but also historical sights and monuments. Raw data has the following view (header and first line):

> | id   | est     | nord    | lat       | lon       | id_tipologia | tipologia                         | id_categoria | categoria_prevalente      | denominazione                                                                                                          | indirizzo                        | localita                      | comune                     | provincia | numero_sedi |
> |------|---------|---------|-----------|-----------|--------------|-----------------------------------|--------------|---------------------------|------------------------------------------------------------------------------------------------------------------------|----------------------------------|-------------------------------|----------------------------|-----------|-------------|
 > | 5001 | 1612472 | 4842033 | 43.722648 | 10.396000 | 3 | Monumento o complesso monumentale | 1 | Arte | BATTISTERO DI PISA | Piazza del Duomo |  | Pisa | PI | 1 |
 >

Each line contains information about a historical/cultural sight: name, type (museum/monument), address (city, street) and GPS cordinates.

**An important observation** about this file is that it begins with two blank lines, which makes the file not compliant to CSV format [RFC 4180](https://datatracker.ietf.org/doc/rfc4180/).

Therefore, we will use two transformers to process this file:
1. [sed-transformer](https://github.com/fusepoolP3/p3-transformer-howto) is used to remove blank lines from the file.
2. [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) to clean the dataset and to transform it into RDF.

#### Cleaning
* Remove blank lines in a file using a stream editor.

* Change entries in "denominazione" column from uppercase to CamelCase: **_BATTISTERO DI PISA -> Battistero Di Pisa_**.

* Apply rounding operation to "lat/lon" columns in order to have 6 digits after decimal point: **_10.39600013248 -> 10.396000_**.

#### RDF mapping
As far as possible the dataset has been mapped to the scheama.org:

* Geo coordinates are described in terms of WGS84
* Addresses are mapped to schemaOrg:Address
* Museum have been described as scheamaOrg:Museum

## Transformed Data

As base URI we use ``` http://fusepool.eu/museum/ ```

A snippet of the resulting transformation:

```turtle
@prefix schema: <http://schema.org/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix geo: <http://www.w3.org/2003/01/geo/wgs84_pos#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

<http://fusepool.eu/museum/id/5001> a rdf:type , schema:Museum ;
	rdfs:label "Battistero Di Pisa" ;
	schema:Location <http://fusepool.eu/museum/location/5001> ;
	schema:VenueType "Monumento o complesso monumentale" ;
	schema:Category "Arte" .

<http://fusepool.eu/museum/location/5001> a rdf:type , schema:Address ;
	geo:lat "43.722648" ;
	geo:long "10.396" ;
	schema:StreetAddress "Piazza del Duomo " .

<http://fusepool.eu/museum/id/5004> a rdf:type , schema:Museum ;
	rdfs:label "Camposanto Monumentale" ;
	schema:Location <http://fusepool.eu/museum/location/5004> ;
	schema:VenueType "Monumento o complesso monumentale" ;
	schema:Category "Arte" .

<http://fusepool.eu/museum/location/5004> a rdf:type , schema:Address ;
	geo:lat "43.722648" ;
	geo:long "10.396" ;
	schema:StreetAddress "Piazza del Duomo " .

<http://fusepool.eu/museum/id/5986> a rdf:type , schema:Museum ;
	rdfs:label "Casa Del Boccaccio" ;
	schema:Location <http://fusepool.eu/museum/location/5986> ;
	schema:VenueType "Museo" ;
	schema:Category "Storia" .

<http://fusepool.eu/museum/location/5986> a rdf:type , schema:Address ;
	geo:lat "43.547405" ;
	geo:long "11.041373" ;
	schema:StreetAddress "Via Boccaccio " .
```

## Transformation Configuration

1. To use [sed-transformer](https://github.com/fusepoolP3/p3-transformer-howto) no special preconfigurations is required. It is configured using the 'script' query parameter. In this query parameter you pass the desired script that is supported by the [GNU sed](https://www.gnu.org/software/sed/manual). For example, to remove empty lines from a file we use the following expression: **'/^\s*$/d'**. An example request is shown below:

```bash
 curl -i -XPOST -H 'Content-Type: text/plain' --data-binary @musei.csv "http://hetzy1.spaziodati.eu:7101?script=/^\s*\$/d"
```

2. To use [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) a transform configuration is rquired. We use GUI of OpenRefine to prepare transformation rules that will be further passed to the transformer in a query parameter. A brief tutorial how to design and extract transformation rules from OpenRefine that can be found [here](https://github.com/andreybratus/tutorial).

Transformation rules are in the form of a JSON array and are saved in a file: [museums_transform.json](https://raw.githubusercontent.com/fusepoolP3/p3-transformer-configs/master/toscana-museums/musei_transform.json).


#### Use of Batchrefine transformer

Transformation rules are passed as a 'refinejson' query parameter to the transformer together with input data:

```bash
	curl -i -XPOST -H 'Content-Type:text/csv' -H 'Accept:text/turtle' --data-binary @musei-noblank.csv "http://hetzy1.spaziodati.eu:7100?refinejson=https://raw.githubusercontent.com/fusepoolP3/p3-transformer-configs/master/toscana-museums/musei_transform.json"
```
http://hetzy1.spaziodati.eu:7100 is a public instance of asynchronous Batchrefine transformer, which would return similar response:

```
HTTP/1.1 100 Continue

HTTP/1.1 202 Accepted
Date: Thu, 29 Jan 2015 15:14:54 GMT
Location: /job/b75afaef-9a26-4cf6-b8f1-78a272b9ff66
Transfer-Encoding: chunked
Server: Jetty(9.2.z-SNAPSHOT)
```

to retrieve data, construct the following request using the job id from Location header: **REMEMBER to use your job id**

```bash
	curl -XGET "http://hetzy1.spaziodati.eu:7100/job/b75afaef-9a26-4cf6-b8f1-78a272b9ff66"
```

## Example Usages of the Data

This dataset can be used to find historical sights situated around (nearby) a given location.
Moreover it can be combined with datasets containing other points of interst in the same region which are also represented by geographical coordinates (accomodations, restaurants).

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX schema: <http://schema.org/>

SELECT ?name ?strAddress ?lat ?long
FROM <http://sandbox.fusepool.info:8181/ldp/toscana-museums-2/museums-new-csv-transformed>
WHERE {
  ?museum schema:address ?address ;
              schema:name ?name ;              
              geo:lat ?lat ;
              geo:long ?long .
  ?address schema:addressRegion "Firenze" ;
           schema:streetAddress ?strAddress .
}
ORDER BY ?name
```


*TODO* Describe how the result data can be used (e.g. by a SPARQL query). This SHOULD also include examples on how this dataset can be combined with other data.
