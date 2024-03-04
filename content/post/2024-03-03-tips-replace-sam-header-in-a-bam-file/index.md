---
title: '[Tips] Replace SAM header in a bam file'
author: Zhenguo Zhang
date: '2024-03-03'
slug: tips-replace-sam-header-in-a-bam-file
categories:
  - Biology
  - Software
tags:
  - Bioinformatics
  - samtools
  - Chromosome number
description: ''
featured_image: ''
images: []
comment: yes
---

Sometimes one need to replace the SAM header lines in a bam file
in order to change the order of chromosomes in the sorted bam file.

**Note** the output of `samtools sort` is determined by the order
of chromosomes in the SAM header (i.e., \@SQ lines), so if in the
header, *chr1* appears after *chr2*, then after sorting, all the
reads aligned to *chr1* will appear after those aligned to *chr2*.

For example, one may want to sort a bam file in the following chromosome
order:

```
chr1
chr2
chr3
chr4
chr5
chr6
chr7
chr8
chr9
chr10
chr11
chr12
chr13
chr14
chr15
chr16
chr17
chr18
chr19
chr20
chr21
chr22
```

What if a bam file's header holds the chromosomes in the following order:

```
chr1
chr10
chr11
chr12
chr13
chr14
chr15
chr16
chr17
chr18
chr19
chr2
chr20
chr21
chr22
chr3
chr4
chr5
chr6
chr7
chr8
chr9
```

Before running `samtools sort`, one need to replace the header of the bam file
with the expected chromosome order.

Here are the correct and wrong ways:

## The correct way

So to replace the header, one can use the following command:

```bash
cat <(samtools view -H new-header.bam) <(samtools view old.bam) | samtools view -b >out.bam
```

The above command replaces the header lines of *old.bam* with that from
*new-header.bam* (of course, you can use any valid header text as header lines).

## The wrong way

```bash
samtools reheader <(samtools view -H new-header.bam) old.bam >out.bam
```

The above command uses `samtools reheader` to replace the headers in the
old bam file. However, this may yield unwanted results.

In a bam file, alignments are stored by referring sequence index: the first
sequence in the header line, second in the header line, etc. If one changes
the second sequence's name from 'chr2' to 'chr11', then all the associated
alignments will have the *chrom* field as 'chr11', and this is exactly what
happens when using `samtools reheader`.

Therefore, if one wants to rename each chromosome name to a different one,
he can use `samtools reheader` as long as the chromosome orders are the same
between the provided chrosome names and the ones in the old bam file. But
if one wants to change the order of the chromosomes in the sorted bam file, 
this would not work.


## References

- A related post: https://www.biostars.org/p/9479497/



