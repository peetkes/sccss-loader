# Run Ingestions and Harmonization flows

## Prerequisites

- java 8+ sdk (preferrably 11 or up)
- marklogic datahub client jar version 5.2.3 (download it here: https://github.com/marklogic/marklogic-data-hub/releases/download/v5.2.3/marklogic-data-hub-5.2.3-client.jar)
- gradle-[environmentName].properties file with appropriate properties set

## Properties file

Create a properties for a specific environment and make sure the following properties are set accordingly:

````
mlHost=localhost
mlUsername=flow-operator
mlPassword=flow-operator-pwd

sccssOntologyLocation=/Users/pkester/Development/SensingClues/sccss-mdh2/data/ontology
sccssConfigurationLocation=/Users/pkester/Development/SensingClues/sccss-mdh2/data/configuration

sccssProject=RewildingApennines
sccssProjectId=9876543
mlcp-Observation-Animal=tab,/Users/pkester/Development/SensingClues/Data/RewildingApennines/multisources
mlcp-Observation-Infrastructure=tab,/Users/pkester/Development/SensingClues/Data/RewildingApennines/fences

mlcp-GeoFeature-Geojson=/Users/pkester/Development/SensingClues/Data/RewildingApennines/geojson

````

Make sure the 'mlcp-' prefixed properties all adhere to the following format:

    mlcp-[entityName]-[entityType]=[delimiter],[location]

Make sure to create an extra [delimiter]-delimit.opt file if a delimiter other than 'comma' or 'tab' is needed.

[comma-delimt.opt]
````
-delimiter
,
````

[tab-delimit.opt]
````
-delimit

````


## Ingestion of data

Run the following command to (re)load the ontology:

    ./gradlew loadOntology -PenvironmentName=[environment]

Run the following command to create a project:

    ./gradlew deployProject -PprojectName=[project name] -PprojectDescription=[project description] -PprojectId=[project identifier] -PenvironmentName=[environment]

Run the following command to ingest data via mlcp into MarkLogic:

    ./gradlew mlcpAllObservations -i -PprojectName=[project name] -PprojectId=[project identifier] -PenvironmentName=[environment]
    ./gradlew mlcpAllMedia -i -PprojectName=[project name] -PprojectId=[project identifier] -PenvironmentName=[environment]
    ./gradlew mlcpAllGeoFeatures -i -PprojectName=[project name] -PprojectId=[project identifier] -PenvironmentName=[environment]

## Run harmonization flows

Run the following command to run step 1 of flow `process-Observations`:

    ./gradlew runFlow -PflowName=process-Observations -Pstep=1

Run the following command to run all steps of flow `process-Observations`:

    ./gradlew runFlow -PflowName=process-Observations -Pstep=1,2,3,4

- step 1: Create Observation entity
- step 2: Enrich Observation entity
- step 3: Correct latitude/longitude coordinates
- step 4: Create geoRegion field in header

Run the following command to run step 1 of flow `process-Media`:

    ./gradlew runFlow -PflowName=process-Media -Pstep=1

Run the following command to run all steps of flow `process-Media`:

    ./gradlew runFlow -PflowName=process-Media -Pstep=1,2,3

- step 1: Create Media entity
- step 2: Link Media entity to Observation entity
- step 3: Create geoRegion field in header

Run the following command to run step 1 of flow `process-GeoFeatures`:

    ./gradlew runFlow -PflowName=process-GeoFeatures -Pstep=1

Run the following command to run all steps of flow `process-GeoFeatures`:

    ./gradlew runFlow -PflowName=process-GeoFeatures -Pstep=1,2,3

- step 1: Create GeoFeature entity
- step 2: Enrich GeoFeature entity
- step 3: Create geoRegion field in header
