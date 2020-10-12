# The DHuS Addon for Aeolus

## Introduction.

The DHuS addon is a Java package that contains the definitions and extraction rules and operations for the Aeolus products. The central element of this package is the ontology definition file ([aeolus.owl](src/main/resources/META-INF/aeolus.owl)) located in the META-INF folder inside the resources directory.

The addon works in conjunction with the DRBX cortex definition (separate package), currently designed to support the following product types from the Aeolus satellite mission:

- ALD\_U\_N\_1B
- ALD\_U\_N\_2B

## Metadata Attributes

All metadata that will be extracted and made available via DHuS is defined within the OWL file mentioned above. The following is a complete list of metadata attributes implemented with this addon. Sample outputs to demonstrate which values these attributes can take can be found in the [samples](src/main/resources/samples) folder inside the resources directory.

| OData Name                   | OpenSearch Name            | Description                                                                          | ALD\_U\_N\_1B | ALD\_U\_N\_2B |
|------------------------------|----------------------------|--------------------------------------------------------------------------------------|---------------|---------------|
| Satellite                    | -                          | Name of the satellite                                                                | &#9745;       | &#9745;       |
| Instrument                   | -                          | Name of the instrument                                                               | &#9745;       | &#9745;       |
| Date                         | -                          | Same as sensing start date                                                           | &#9745;       | &#9745;       |
| Sensing start                | beginposition              | Sensing start date                                                                   | &#9745;       | &#9745;       |
| Sensing stop                 | endposition                | Sensing stop date                                                                    | &#9745;       | &#9745;       |
| Footprint                    | gmlfootprint               | GML footprint                                                                        | &#9745;       | &#9745;       |
| JTS footprint                | footprint                  | JTS footprint                                                                        | &#9745;       | &#9745;       |
| Platform name                | platformname               | Name of the platform                                                                 | &#9745;       | &#9745;       |
| Platform short name          | platformshortname          | Short name of the platform                                                           | &#9745;       | &#9745;       |
| Platform serial identifier   | platformserialidentifier   | Identification number of the platform among the mission                              | &#9745;       | &#9745;       |
| Platform NSSDC identifier    | platformnssdcidentifier    | The National Space Science Data Center (NSSDC) identification number of the platform | &#9745;       | &#9745;       |
| Instrument name              | instrumentname             | Name of the instrument                                                               | &#9745;       | &#9745;       |
| Instrument short name        | instrumentshortname        | Short name of the instrument                                                         | &#9745;       | &#9745;       |
| Orbit number                 | orbitnumber                | Absolute orbit number                                                                | &#9745;       | &#9745;       |
| Relative orbit number        | relativeorbitnumber        | Relative orbit number                                                                | &#9745;       | &#9745;       |
| Cycle                        | cycle                      | Number of cycles                                                                     | &#9745;       | &#9745;       |
| Phase                        | phase                      | Mission phase                                                                        | &#9745;       | &#9745;       |
| Processing level             | processinglevel            | The value of the last processing                                                     | &#9745;       | &#9745;       |
| Processing center            | processingcenter           | Processing center ID                                                                 | &#9745;       | &#9745;       |
| Processor version            | processorversion           | Version number of processing software                                                | &#9745;       | &#9745;       |
| Baseline                     | processingbaseline         | Processing Baseline                                                                  | &#9745;       | &#9745;       |
| Generation time              | -                          | Product generation date                                                              | &#9745;       | &#9745;       |
| Processing mode              | processingmode             | Full name of processing mode or file class                                           | &#9745;       | &#9745;       |
| Processing mode abbreviation | processingmodeabbreviation | Abbreviated name of processing mode or file class                                    | &#9745;       | &#9745;       |
| Product type                 | producttype                | Product type designation                                                             | &#9745;       | &#9745;       |
| Product description          | productdescription         | One line description of the file                                                     | &#9745;       | &#9745;       |
| Size                         | size                       | File size                                                                            | &#9745;       | &#9745;       |
| Format                       | format                     | Product format description                                                           | &#9745;       | &#9745;       |
| Filename                     | filename                   | File name                                                                            | &#9745;       | &#9745;       |


More information on the source of the respective attributes can be found directly by looking at the OWL file itself.

## Footprint Extraction

In contrast to all other attributes, the footprint coordinates are extracted from the binary data file (DBL). The cortex topic (separate package) uses an XML schema definition file to create a tree of nodes which is then navigable within the addon OWL.

### Latitudes and Longitudes

The structure of the binary data files is different for Level-1 and Level-2 data. The generation of the footprints is based on the following sources:

- ALD\_U\_N\_1B: Latitude and Longitude of the intersection of DEM and the line-of-sight (Table 5-10 in [Aeolus Level 1b Input/Output Data Definitions Interface Control Document](https://earth.esa.int/pi/esa?type=file&table=aotarget&cmd=image&alias=Aeolus_L1B_Input_Output_DD_ICD))
- ALD\_U\_N\_2B: Latitude and Longitude of DEM intersection for the CoG of the current accumulation (Table 26 in [Aeolus Level 2b/2c Input/Output Data Definitions Interface Control Document](https://earth.esa.int/pi/esa?type=file&table=aotarget&cmd=image&alias=Aeolus_L2B_2C_Input_Output_DD_ICD))

The values are provided in the unit of microdegrees and need to be scaled accordingly. Furthermore, longitudes need to be converted from the (0-360) range to the (-180 to 180) range.

### Geometry

One footprint covers an entire orbit. The ALADIN instrument measures vertical profiles that are repeated at intervals of 200 km. Therefore, a scattered point visualization would be appropriate to represent the acquisition geometry, however, a simple way to do this with DHuS was not found. Instead, a continuous polygon having a fictional width of 1 millidegree was chosen (defined by the offset variable in the OWL).

The number of points per polygon needs to be small enough for the DHuS to be able to handle it. A total number of approximately 400 points was chosen.

### Issues

Currently, footprint generation takes a long time. At some point, optimization might become necessary.

