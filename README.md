# SwissDRG AG Grouper Documentation

## Introduction
The JavaGrouper 1.1.0 is a reimplementation of the older C based grouper kernel. 
The grouper logic remains the same. However there are some differences to the old grouper kernel,
 especially regarding non-functional features:

- The new grouper kernel is fully based on Java; insofar it is platform independent.
- The new grouper kernel supports concurrent grouping of patient cases. Once loaded, a grouper kernel
  can be used in concurrent environments without further measures.
- The old binary grouper specifications can no longer be used.
  The [new specifications](/pages/specifications.md) are in JSON. 
  
Java version: 1.6

## TOC
* [Specifications](pages/specifications.md)
* [Grouping](pages/grouping.md)    
* [Batchgrouping](pages/batchgrouping.md)
    * [Input and Output Formats](#input-and-Output-Formats)
* [Supplement Grouping](pages/supplement-grouping.md)

## Input and Output Formats

The Batch Grouper requires/supports several **input formats**:
* [Batchgrouper 2017 Format](pages/format-batchgrouper-2017.md)
* Batchgrouper Input Format, see section 1.1 of this document:   
  [DE](https://docs.swissdrg.org/grouper-doku-de.pdf), 
  [FR](https://docs.swissdrg.org/grouper-doku-fr.pdf), 
  [IT](https://docs.swissdrg.org/grouper-doku-it.pdf)
* BFS Format: the official BAG format as defined 
  [here](https://www.bfs.admin.ch/bfs/de/home/statistiken/gesundheit/erhebungen/ms.assetdetail.1922896.html)
* URL Patient Case Format: <small>Coming Soon <sup>tm</sup></small>    

The batchgrouper produces one **output format**. It is described in section 1.3 of the already mentioned
document here:              
[DE](https://docs.swissdrg.org/grouper-doku-de.pdf), 
[FR](https://docs.swissdrg.org/grouper-doku-fr.pdf), 
[IT](https://docs.swissdrg.org/grouper-doku-it.pdf)
