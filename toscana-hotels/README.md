Transformer configuration for the Tuscany Accommodations dataset
--------------------------------------------------------

## Input Data

As input data we have a CSV file provided by the Tuscany region, namely [strutture_ricettive_20141012.csv](http://dati.toscana.it/dataset/ceb33e9c-7c80-478a-a3be-2f3700a64906/resource/5e8ec560-cbe6-4630-b191-e274218c183c/download/strutturericettive20141012.csv)

>http://dati.toscana.it/dataset/rt-strutric

This CSV dataset contains the names and contact details (address, phone, email, website) of all tourist accommodation in Tuscany, and includes ISTAT (Italian National Institute of Statistics) codes, accommodation type (hotels, cottages, campsites), star rating and the establishment's geo-coordinates.

Raw data has the following view (header and two lines)

>| id   | codeserc      | tipologia   | nome      | indirizzo                         | cap   | citta    | provincia | stelle | email               | url             | lt    | lg    | telefono    |
>|------|---------------|-------------|-----------|-----------------------------------|-------|----------|-----------|--------|---------------------|-----------------|-------|-------|-------------|
>| 7786 | 050025AAT0017 | Agriturismi | I MORICCI | Via di Ripassaia, 10 - - Fabbrica | 56037 | Peccioli | PI        | 2      | imoricci@tiscali.it | www.imoricci.it | 41.00 | 13.00 | 0587 697446 |
>| 1158 | 053018ALB0011 | Alberghi - Hotel | CORTE DEI BUTTERI | S.Statale Aurelia 229/231 - OSA - FONTEBLANDA | 58010 | Orbetello | GR | 4 | info@aurumhotels.it | www.aurumhotels.it | 42.564893699999998943894752301275730133056640625 | 11.1714807999999994336803865735419094562530517578125 | 0564 885546 |

For this dataset we use two Fusepool transformers:

1. **Cleaning:** [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) to clean the dataset.

2. **Transform to RDF:** Virtuoso CSV transformer http://fusepool.openlinksw.com/ext/csv to transform the data into RDF.

## Transformed data

As far as possible the input has been mapped to schema.org types.
* Addresses are mapped to schemaOrg:PostalAddress
* Geo coordinates are described in terms of both WGS84 lat/long and schema.org GeoCoordinates.
* Each accommodation is of type schemaOrg:LodgingBusiness, hotels are also of type schemaOrg:Hotel
** schema.org does not define types for many of the accommodation types in the source dataset. A custom ontology would be required to capture these.

```rdf

<rdf:RDF
 	...
	xmlns:geo="http://www.w3.org/2003/01/geo/wgs84_pos#"
	xmlns:schema="http://schema.org/"
	xmlns:oplbase="http://www.openlinksw.com/schemas/oplbase#"
	xmlns:ns10="http://fusepool.openlinksw.com/about/id/http/fusepool.openlinksw.com/tuscany/accommodation#" >
	...
 <rdf:Description rdf:about="http://fusepool.openlinksw.com/tuscany/accommodation#Accommodation_1158">
    <rdf:type rdf:resource="http://schema.org/Hotel" />
    <rdf:type rdf:resource="http://schema.org/LodgingBusiness" />
    <rdfs:label>Corte Dei Butteri</rdfs:label>
    <geo:lat>42.564894</geo:lat>
    <geo:long>11.171481</geo:long>
    <schema:name>Corte Dei Butteri</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/tuscany/accommodation" />
    <oplbase:id>1158</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/tuscany/accommodation#Accommodation_1158_Addr" />
    <schema:description>Alberghi - Hotel</schema:description>
    <schema:url>www.aurumhotels.it</schema:url>
    <schema:geo rdf:resource="http://fusepool.openlinksw.com/tuscany/accommodation#Accommodation_1158_GeoCoords" />
    <ns10:codeserc>053018ALB0011</ns10:codeserc>
    <ns10:stelle>4</ns10:stelle>
  </rdf:Description>
  <rdf:Description rdf:about="http://fusepool.openlinksw.com/tuscany/accommodation#Accommodation_1178">
    <rdf:type rdf:resource="http://schema.org/Hotel" />
    <rdf:type rdf:resource="http://schema.org/LodgingBusiness" />
    <rdfs:label>Hotel Fontermosa</rdfs:label>
    <geo:lat>42.564894</geo:lat>
    <geo:long>11.171481</geo:long>
    <schema:name>Hotel Fontermosa</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/tuscany/accommodation" />
    <oplbase:id>1178</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/tuscany/accommodation#Accommodation_1178_Addr" />
    <schema:description>Alberghi - Hotel</schema:description>
    <schema:url>www.fontermosa.it</schema:url>
    <schema:geo rdf:resource="http://fusepool.openlinksw.com/tuscany/accommodation#Accommodation_1178_GeoCoords" />
    <ns10:codeserc>053018ALB0031</ns10:codeserc>
    <ns10:stelle>4</ns10:stelle>
  </rdf:Description>
```

## Transformation Configuration


#### 1. Clean and prepare data

**Cleaning** with [Batchrefine](https://github.com/fusepoolP3/p3-batchrefine)

 * change entries in columns "nome" and "indirizzo" from uppercase to titlecase **_I MORICCI --> I Moricci_**  
 * apply rounding to "lt/lg" columns to 6 digits after decimal point: **_10.39658901498329400 -> 10.396589_**  
 * in the dataset, if number of stars is not applicable to the accommodation facility, column "stelle" has value 0. In this case we want to replace it with a blank cell.  

To use [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) a transform configuration is rquired. We use GUI of [OpenRefine](https://github.com/OpenRefine/OpenRefine) to prepare transformation rules that will be further passed to the transformer in as a query parameter. A brief tutorial how to design and extract transformation rules from OpenRefine can be found [here](https://github.com/andreybratus/tutorial).

Transformation rules are extracted from OpenRefine and saved in a JSON file, which we also provide: [strutture-transform.json](strutture-transform.json)

Pass transformation rules as a 'refinejson' query parameter to batchrefine together with the input data:

```bash
curl -i -XPOST -H 'Content-Type:text/csv' -H 'Accept:text/csv' --data-binary @strutturericettive20141012.csv "http://hetzy1.spaziodati.eu:7100/?refinejson=https://raw.githubusercontent.com/fusepoolP3/p3-transformer-configs/master/toscana-hotels/strutture-transform.json"
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

expected result after cleaning is [strutturericettive-cleaned.csv](strutturericettive-cleaned.csv), which we will use to pass to Virtuoso CSV transformer. 

#### 2. Transform cleaned data to RDF

**Transform to RDF** using Virtuoso CSV transformer

To transform cleaned data to RDF with Virtuoso CSV transformer use the following request:

```bash
curl -i -H "Content-Type: text/csv" -H "Accept: application/rdf+xml" -H "Content-Location: http://fusepool.openlinksw.com/tuscany/accommodation" --data-binary @strutturericettive-cleaned.csv -X POST "http://fusepool.openlinksw.com/ext/csv"
```
which would return similar response:

```
HTTP/1.1 100 Continue

HTTP/1.1 202 Accepted
Server: Virtuoso/07.10.3211 (Linux) x86_64-redhat-linux-gnu  VDB
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8
Date: Fri, 30 Jan 2015 19:15:36 GMT
Accept-Ranges: bytes
Location: /ext/status/120
Content-Length: 
```

to retrieve data, construct the following request using the job id from Location header: **REMEMBER to use your job id**

```bash
curl -X GET "http://fusepool.openlinksw.com/ext/status/120"
```

The expected result is [strutturericettive-cleaned.rdf](strutturericettive-cleaned.rdf)

The Virtuoso CSV transformer requires no end-user configuration, insofar as the transformation is expressed in a developer-defined XSLT stylesheet (tuscanyaccommodations2rdf.xsl). The stylesheet and supporting Virtuoso/PL code are packaged and installed via a Fusepool-specific Cartridges VAD. Use of a particular stylesheet for transformation of a specific CSV dataset is triggered by associating the header line of the CSV data with the .xsl stylesheet. This association is set up through the 'CSV Pattern configuration' dialog in Virtuoso's Conductor UI, as depicted in the screenshots below.

<div align="center">
<img src="img/csv_header_config1.png" alt="Header config" width="75%" height=75%>
</div>

<div align="center">
<img src="img/csv_header_config2.png" alt="Header config" width="75%" height=75%>
</div>

## Example Usages of the Data

The "Tuscany Region - Accommodations" dataset archive could provide the basis for online services supporting the searching and booking of tourist accommodation. It is of particular interest as it represent accomodation venues of various types. The geo-references can be exploited to represent establishments on a map.

An example SPARQL query retrieves all the entries (hotels) that have "FI" as their province code and shows their "Name, address, and geo cordinates"

```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX schema: <http://schema.org/>

SELECT ?name ?strAddress ?lat ?long
FROM <http://sandbox.fusepool.info:8181/ldp/toscana-accommodations-1/ly-csv-transformed>
WHERE {
  ?accommodation schema:address ?address ;
                 schema:name ?name ;
                 geo:lat ?lat ;
                 geo:long ?long .
  ?address schema:addressRegion "FI" ;
           schema:streetAddress ?strAddress .
}
ORDER BY ?name
```
