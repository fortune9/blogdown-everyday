---
title: 'UCSC Genome Mappability: a resource for analyzing NGS data'
author: Zhenguo Zhang
date: '2018-08-31'
slug: ucsc-genome-mappability-a-resource-for-analyzing-ngs-data
categories:
  - Biology
tags:
  - Research
  - sequencing
---

Often, mapping sequenced reads to a reference genome is the first step
of analyzing next-generation sequencing data. However, a genome may
contain many pieces of similar regions, making the reads derived from
these similar regions difficult to map back -- having no idea which
region they are from. But with the information of similar regions in mind,
one may pay attention to such regions and make data analysis clearer.

In fact, the UCSC genome browser has provided such resources: 
[Mappability and Uniqueness of genomes](http://genome.ucsc.edu/cgi-bin/hgFileUi?db=hg19&g=wgEncodeMapability).

There are three types data here, which I summarize in the below table:

Type | Generation Method
---: | :---
--- |
Alignability | Using GEM-mappability and allowing up to 2 mismatches, the uniqness of each k-mer is evaluated, in the formula S=1/(number of mapped places)
--- |    
Uniqueness | the same as Alignability, but no mismatches are allowed.
--- |
Blacklisted | 229 manually curated regions with lots of reads mapped regardless of tissues, defined with chromatin accessibility and chip-seq data.

## Key notes

1. the 'Blacklisted' regions exclude genic or promoter regions,
and it has little overlap with low alignable regions, so it is recommended
to use both lists for analyses.

2. the 'Blacklisted' regions are based on chromatin and chip-seq
data, so it may not be effective for RNA-seq data.

3. the scores (range from 0 to 1) for 'Alignability' and 'Uniqueness'
are assigned to the first base of a k-mer, not the middle one.

## References

1. GEM-mappability: the [program](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0030377) used for generating alignability data.

2. This [link](https://personal.broadinstitute.org/anshul/projects/encode/rawdata/blacklists/hg19-blacklist-README.pdf) describes how the blacklisted regions were generated. And this [link](http://mitra.stanford.edu/kundaje/akundaje/release/blacklists/) directs you to the site for downloading the blacklisted regions.

3. The data for human hg19 can be downloaded [here](http://genome.ucsc.edu/cgi-bin/hgFileUi?db=hg19&g=wgEncodeMapability)

