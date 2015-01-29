Transformer configuration for Museums dataset
---------------------------------------------

## Input Data

As input data we use a CSV file provided by the Toscana region, namely [musei.csv](http://mappe.regione.toscana.it/db-webgis/musei/example_postgis.jsp?format=csv).
> http://mappe.regione.toscana.it/db-webgis/musei/example_postgis.jsp?format=csv

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

The vocabularie used in the mapping are:

> rdf    <http://www.w3.org/1999/02/22-rdf-syntax-ns#>   
> rdfs	 <http://www.w3.org/2000/01/rdf-schema#>   
> geo	 <http://www.w3.org/2003/01/geo/wgs84_pos#>   
> schema <http://schema.org/>


*TODO*
* Describe the RDF schema / ontology used for the output data.
* Provide some RDF snippet of transformed RDF data

## Transformation Configuration

*TODO* Describe the Transformer configuration used to transform the source data. This includes listing the specific transformers including their configuration (e.g. providing a link to the JSON file used for Batch Refine)

## Example Usages of the Data

*TODO* Describe how the result data can be used (e.g. by a SPARQL query). This SHOULD also include examples on how this dataset can be combined with other data.
