# Grouper Specifications and Catalogs

## Specifications

The JavaGrouper groups patients into DRGs according to the SwissDRG "Specifications": a set of rules 
that define which patient goes into which DRG. 

The specifications are published three times a year in the [Download Portal](https://download.swissdrg.org).

Each specification is a set of JSON files contained in one folder. The folder is named 

    <major version>.<minor version>   // for SwissDRG   
    t<major version>.<minor version>  // for TARPSY     
    
where "major version" means either SwissDRG or TARPSY version (from 1 to currently 7 for the former);
"minor version" is a integer between 0 and 3:

- 0 = Catalog version
- 1 = Planning version 1
- 2 = Planning version 2
- 3 = Billing version

Examples:

- 7.3 = SwissDRG V7.0, Billing version
- t1.2 = TARPSY V1.0, planning version 2

In the Download Portal, there is a special [Zip archive](https://download.swissdrg.org/specs/28)
that contains all JSON specifications for SwissDRG versions V1.0 to V6.0.
  
The JSON specifications for the **newer versions** are also in the Download Portal under 
[Spezifikationen](https://download.swissdrg.org/specs): open the Zip archive of a given version and 
therein use the folder named e.g. 7.3 (meaning as described above).

**Note:** the old C-Grouper used a binary format of the specifications (not JSON). These are also 
included in the Zip archives of SwissDRG versions 1 to 7 (but not anymore in later versions).
    
## Catalogs

To group, the JavaGrouper needs the catalog: the list of DRGs (or "PCG", in the case of TARPSY) that
contains for each DRG their cost weights, among other information.

The catalogs are CSV files, included in the Zip archive of the specifications.                               