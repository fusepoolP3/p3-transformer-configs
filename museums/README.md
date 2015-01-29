Transformer configuration for Museums dataset
---------------------------------------------

## Input Data

As input data we use a CSV file provided by the Toscana region, namely [musei.csv](https://raw.githubusercontent.com/andreybratus/p3-transformer-configs/master/museums/musei.csv).
> http://mappe.regione.toscana.it/db-webgis/musei/example_postgis.jsp?format=csv

>**An important notice** about this file is that it starts with two blank lines, which makes the file not compliant to CSV format [RFC 4180](https://datatracker.ietf.org/doc/rfc4180/). Currently I assume that we remove them manually, another possible workarounds:
>* Polish, fix and document [sed-transformer](https://github.com/fusepoolP3/p3-transformer-howto) that we can use to remove blank lines form files.

This dataset aims at uncovering cultural locations in the region, mainly museums but also historical sights and monuments. Raw data has the following view (header and first line):

> | id   | est     | nord    | lat       | lon       | id_tipologia | tipologia                         | id_categoria | categoria_prevalente      | denominazione                                                                                                          | indirizzo                        | localita                      | comune                     | provincia | numero_sedi |
> |------|---------|---------|-----------|-----------|--------------|-----------------------------------|--------------|---------------------------|------------------------------------------------------------------------------------------------------------------------|----------------------------------|-------------------------------|----------------------------|-----------|-------------|
 > | 5001 | 1612472 | 4842033 | 43.722648 | 10.396000 | 3 | Monumento o complesso monumentale | 1 | Arte | BATTISTERO DI PISA | Piazza del Duomo |  | Pisa | PI | 1 |
 >

Each line represents information about the historical/cultural sight: name, type (museum/monument), address (city, street) and GPS cordinates.

We use Batchrefine transformer to clean the dataset and to transform it into RDF:
* change entries in "denominazione" column to CamelCase: **_BATTISTERO DI PISA -> Battistero Di Pisa_**

* apply rounding to "lat/lon" columns to 3 digits after decimal point: **_10.396000 -> 10.396_**

For each of the Historic sights we extract and map to RDF the following information:

* Name of the location
* Address
* Category
* Geographic coordinates (lat, long)

## Transformed Data

The vocabularies used in the mapping are:

```
 rdf     <http://www.w3.org/1999/02/22-rdf-syntax-ns#>   
 rdfs	 <http://www.w3.org/2000/01/rdf-schema#>   
 geo	 <http://www.w3.org/2003/01/geo/wgs84_pos#>   
 schema  <http://schema.org/>
```

As base URI we use ``` http://fusepool.eu/museum/ ```
A snippet of the resulting transformation:

```
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
	geo:lat "43.723" ;
	geo:long "10.396" ;
	schema:StreetAddress "Piazza del Duomo " .

<http://fusepool.eu/museum/id/5004> a rdf:type , schema:Museum ;
	rdfs:label "Camposanto Monumentale" ;
	schema:Location <http://fusepool.eu/museum/location/5004> ;
	schema:VenueType "Monumento o complesso monumentale" ;
	schema:Category "Arte" .

<http://fusepool.eu/museum/location/5004> a rdf:type , schema:Address ;
	geo:lat "43.723" ;
	geo:long "10.396" ;
	schema:StreetAddress "Piazza del Duomo " .
```

## Transformation Configuration

To use Batchrefine transformer a transform configuration is rquired. We use GUI of OpenRefine to design transformation rules that will be further passed to batchrefine transformer in a query parameter. A brief tutorial how we can do that can be found [here](https://github.com/andreybratus/tutorial).

Transformation rules get extracted from OpenRefine and saved in a JSON file, which we also provide: [museums_transform.json](https://raw.githubusercontent.com/andreybratus/p3-transformer-configs/master/museums/museums_transform.json).

Pass transformation rules as a 'refinejson' query parameter to batchrefine together with the input data:

	curl -i -XPOST -H 'Content-Type:text/csv' -H 'Accept:text/turtle' --data-binary @/home/andrey/musei.csv "http://hetzy1.spaziodati.eu:7100?refinejson=https://raw.githubusercontent.com/andreybratus/p3-transformer-configs/master/museums/museums_transform.json"

http://hetzy1.spaziodati.eu:7100 is a public instance of asynchronous transformer, which would return similar response:

```
HTTP/1.1 100 Continue

HTTP/1.1 202 Accepted
Date: Thu, 29 Jan 2015 15:14:54 GMT
Location: /job/b75afaef-9a26-4cf6-b8f1-78a272b9ff66
Transfer-Encoding: chunked
Server: Jetty(9.2.z-SNAPSHOT)
```
to retrieve data from it construct the following request using the job id from Location header: **REMEMBER to use your job id**

	curl -XGET "http://hetzy1.spaziodati.eu:7100/job/b75afaef-9a26-4cf6-b8f1-78a272b9ff66"


	
## Example Usages of the Data

*TODO* Describe how the result data can be used (e.g. by a SPARQL query). This SHOULD also include examples on how this dataset can be combined with other data.
