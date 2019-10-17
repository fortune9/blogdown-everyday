---
title: '[Bioinfo] Extract data using NCBI E-utilities'
author: Zhenguo Zhang
date: '2019-10-16'
slug: bioinfo-extract-data-using-ncbi-e-utilities
categories:
  - Biology
  - Computing
tags:
  - Resource
---

Today I am going to introduce a powerful tool to retrieve data from
[NCBI](https://www.ncbi.nlm.nih.gov/) -- [E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25501/). This is
a REST API for NCBI databases. I used the API several years ago, but
recently I picked it up again for my projects, so I think that this
is a good opportunity to make some records for my future use as well as
for internet users. So let's start.

## Introduction

The use of the API has the following format:

```html
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term=science[journal]+AND+breast+cancer+AND+2008[pdat]&retmode=json
```

The base URL is *https://eutils.ncbi.nlm.nih.gov/entrez/eutils/*,
followed by a E-utilities tool (esearch.fcgi in the above example),
and then by arguments: each argument has the format *name=value*,
and arguments are combined with '&', while the *value* uses '+'
whenever a space is needed. The arguments of each tool are introduced
at <https://www.ncbi.nlm.nih.gov/books/NBK25499/>.

Now, I will brief all available E-utilities tools and may write some
advanced uses in future.

## Nine tools

There are 9 tools in this kit. Here is a summary table:

Name | Description | Example
--- | --- | ---
einfo | get a list of databases or statistics of a given database | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/einfo.fcgi?db=protein&version=2.0
esearch | get a list of UIDs for a search term and can store results on history server if userhistory=y | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term=cancer&retmax=100&usehistory=y
esummary | return document summaries for provided lists or those stored at history server, can use *retstart* and *retmax* to limit returned items | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esummary.fcgi?db=pubmed&id=11850928,11482001
efetch | similar to esummary, but for returning more complicated data | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&id=17284678,9997&retmode=text&rettype=abstract
elink | return a list of related UIDs for input UIDs, or list the related links, depending on the parameter *cmd* | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/elink.fcgi?dbfrom=pubmed&id=19880848,19822630&cmd=acheck
epost | upload id lists to history server, and combine with previous lists if WebEnv is provided | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/epost.fcgi?db=pubmed&id=11237011,12466850 
EGQuery | record counts from all Entrez databases by searching a term | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/egquery.fcgi?term=asthma
ESpell | get suggestion for searching terms, such as correcting typos | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/espell.fcgi?db=pubmed&term=asthmaa+OR+alergies
ECitMatch | get citation counts for a paper, which has to be given in the format of *journal_title\|year\|volume\|first_page\|author_name\|your_key\|*, *your_key* is an arbitary string for identify the results | https://eutils.ncbi.nlm.nih.gov/entrez/eutils/ecitmatch.cgi?db=pubmed&retmode=xml&bdata=proc+natl+acad+sci+u+s+a|1991|88|3248|mann+bj|Art1|%0Dscience|1987|235|182|palmenberg+ac|Art2|

See more details on parameters for each tool and examples at <https://www.ncbi.nlm.nih.gov/books/NBK25499/>.

## Two approaches

Actually, there are two approaches to use the E-utilities tools:

1. Use the URL directly.  This
constructs the URLs as above and then retrieve the results. This can
be done within other programming languages such as Perl or Bash, see
<https://www.ncbi.nlm.nih.gov/books/NBK25498/> for Perl examples.


2. Use Entrez Direct toolkit. This approach wraps constructing URLs and
retrieving results into a program *edirect* -- one program of this
toolkit, and other programs accept similar parameters as the direct use
and use the *edirect* program to process the request. The available
programs are: *einfo,esearch,esummary,efetch,epost,elink,espell,ecitmatch*.
In addition, there is one more program **_xtract_**, which can parse XML
output, so provides a convenient way to extract results. This toolkit
can be installed under Linux using *apt install ncbi-entrez-direct*

## Frequency of requests

To avoid overload the NCBI server, it has the following limitations on
the frequency of making requests from any IP address:

* default: no more than 3 requests can be made per second. Violating this
may lead to blocking the IP.

* API Key: from December 2018, users can use an API key to increase their
request frequency to 10/sec. To get the key, go to the page <https://www.ncbi.nlm.nih.gov/account/settings/>.
Then append the key to request URL like

  ```html
  esummary.fcgi?db=pubmed&id=123456&api_key=ABCDE12345
  ```

One can also send email to <mailto:eutilities@ncbi.nlm.nih.gov> to
register his tool by providing the **tool** name and **email**. After 
that one can use the registered tool and email when making requests,
which will help to increase the request frequency.

## Other notes

* one can use argument *retmode* to determine results format: xml or json.

* when the number of records is greater than 10000, it seems that the
efetch and esummary tools from the ncbi-entrez-direct toolkit will fail,
possibly caused by too frequent requests.

In summary, E-utilities is really useful toolkit for retrieving information
from NCBI. I wish I have time to share more on their applications
in future posts.

Have a nice day :smiley:
