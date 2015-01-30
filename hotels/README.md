Transofrmer configuration for the Accomodations dataset
--------------------------------------------------------

## Input Data

As input data we have a CSV file provided by the Toscana region, namely [strutture_ricettive_20141012.csv](http://dati.toscana.it/dataset/ceb33e9c-7c80-478a-a3be-2f3700a64906/resource/5e8ec560-cbe6-4630-b191-e274218c183c/download/strutturericettive20141012.csv)

>http://dati.toscana.it/dataset/rt-strutric

This dataset provides information about hospitality facilities in the region. Raw data has the following view (header and first line)

>| id   | codeserc      | tipologia   | nome      | indirizzo                         | cap   | citta    | provincia | stelle | email               | url             | lt    | lg    | telefono    |
>|------|---------------|-------------|-----------|-----------------------------------|-------|----------|-----------|--------|---------------------|-----------------|-------|-------|-------------|
>| 7786 | 050025AAT0017 | Agriturismi | I MORICCI | Via di Ripassaia, 10 - - Fabbrica | 56037 | Peccioli | PI        | 2      | imoricci@tiscali.it | www.imoricci.it | 41.00 | 13.00 | 0587 697446 |

Each line of the dataset represents a hospitality facility, its characteristics (type, number of stars), location and contact information.

We use Batchrefine transformer to clean the dataset and to transform it into RDF:

* change entries in nome coluumn to CamelCase **_I MORICCI --> I Moricci_**

* apply rounding to "lt/lg" columns to 3 digits after decimal point: **_10.396000 -> 10.396_**
* in the dataset if number of stars is not applicable to the accomodation facility, column "stelle" has value 0. In this case we make the cell blank, to avoid confusion.

* for each accomodation facility we extract and map to RDF the following information:

..* Name
..* Type and number of stars
..* Address (City, Street, postal code)
..* Contact information (email, phone number)

## Transformed Data

The vocabularie used in the mapping are:

> rdf    <http://www.w3.org/1999/02/22-rdf-syntax-ns#>   
> rdfs	 <http://www.w3.org/2000/01/rdf-schema#>   
> geo	 <http://www.w3.org/2003/01/geo/wgs84_pos#>   
> schema <http://schema.org/>
