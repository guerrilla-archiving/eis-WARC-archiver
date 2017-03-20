# EIS Archiver
This repository will describe the tools used to successfully archive a given set of webpages. This is developed for EPA's EIS documents serving catalogue, but it is generalized enough to be used for any webpage. 

The process involves 3 major steps:

1.	Generating a list of URLs to be archived.
2.	Running the [grab-site](https://github.com/slang800/grab-site) for the list of URLs as a warcfactory process that is constantly generating WARC format files for the supplied URLs.
3.	Verifying the quality of the generated WARCs using [warcat](https://github.com/chfoo/warcat)
4.	Optional wayback machine [pywb](https://github.com/ikreymer/pywb) that be used to playback the generated WARCs.


# Dependencies
Install the following dependencies in your machine before getting started with the harvesting proccess.

List of URLs
---
The repository consists of a basic URL generator `csv2Urls.py`. It just concatenates a base URL with a value from the supplied csv to form a valid URL. The output is a text file newline delimited that can be supplied to the grab-site docker app. See example files `eis-listing.csv` and `paths.txt` for reference.

**Usage**

Script takes in 3 arguments:
- Path to the csv
- Key/Column name in the csv
- Base

```bash
python csv2Urls.py [path/to/csv] [key name in the csv to concatenate] [base url]
```

OR use your own way of generating URLs, the app just needs a text file of URLs newline delimited.

Docker
---
Install the Docker Engine on your machine, its available for Mac/Win/Linux, see [instructions](https://docs.docker.com/engine/installation/) for installation specific to your environment. Although this process has been only tested on Mac/Linux, it requires a bash shell. This process hasn't been tested for a Windows environment with or without bash shell.

# Usage
Once the dependencies are installed, specifically the Docker environment then you are ready to use grab-site 

grab-site
---
[![Travis](https://img.shields.io/travis/rust-lang/rust.svg)](https://hub.docker.com/r/slang800/grab-site/) [![Docker](https://img.shields.io/badge/docker--repo-pull-blue.svg)](https://hub.docker.com/r/slang800/grab-site/)

Full install/reference [instructions](https://github.com/slang800/grab-site).

Get the pre-built docker container:
```bash
docker pull slang800/grab-site
```

Running
---
To run  grab-site, enter the following command in the terminal:
Can set the port to whatever you want after `-p` flag.
Set Volume path for data output (ie WARCs) after `-v` flag up to `:/data`

```bash
docker run --detach -p [port]:[port] -v [path-to-folder-to-store-data]/grab-site-date:/data --name warcfactory slang800/grab-site
```

Verify the container running using:

```bash
docker ps
```
![here](/docs/img/docker-ps.png)

The container `warcfactory` should appear on the output.

# Starting a crawl

For a single URL crawl use the following command:

```bash
docker exec warcfactory grab-site --no-offsite-links http://example.com
```

Supplied in this repo is a shell script `runWarcfactory.sh` that just reads in a text file of URLs and runs the above command for each and every URLs. 

To run the shell script in background and with no outputs run it as:

```bash
nohup sh runWarcfactory.sh [path_to_text_file_of_URLS] &
```

The crawling will be done automatically for each URLs supplied in the text file.
If your machine is setup or exposed to be a public ip, you can monitor it at `localhost:[port]`, the [port] being the port the grab-site is running on. The dashboard will show current progress and status of the crawl.

# Tools for verification of WARCS

Below are some tools to verify the validity of the WARC files generated by `grab-site`
TODO: Write usage instructions for `pywb`
warcat

[![Travis](https://img.shields.io/travis/rust-lang/rust.svg)](https://travis-ci.org/chfoo/warcat)

Install warcat using pip or source [here](https://github.com/chfoo/warcat).

warcat Quick Installation and usage
-----------------------------------------
- Only Python3 compatible, Install pip3 via ```sudo apt-get install python3-pip```
- Install warcat via ```pip3 install warcat```.
- To verify a valid WARC file - ```python3 -m warcat verify megawarc.warc.gz --progress```
- To extract files from a WARC file - ```python3 -m warcat extract megawarc.warc.gz --output-dir /tmp/megawarc/ --progress```

pywb
---
[![Travis](https://img.shields.io/travis/rust-lang/rust.svg)](https://travis-ci.org/ikreymer/pywb) [![Coverage Status](https://coveralls.io/repos/github/ikreymer/pywb/badge.svg?branch=master)](https://coveralls.io/github/ikreymer/pywb?branch=master)

This tool can playback WARC files on your localhost, this can be used to verify the generated WARCs, full installation instructions [here](https://github.com/ikreymer/pywb)

# Definitions

## EPA = Environmental Protection Agency
EPA is an agency created for the purpose of protecting human health and the environment by writing and enforcing regulations based on laws passed by Congress.

The agency conducts environmental assessment, research, and education. It has the responsibility of maintaining and enforcing national standards under a variety of environmental laws, in consultation with state, tribal, and local governments.

Source - https://en.wikipedia.org/wiki/United_States_Environmental_Protection_Agency

## EIS = Environemntal Impact Statement

EIS, under United States environmental law, is a document required by the National Environmental Policy Act (NEPA) for certain actions "significantly affecting the quality of the human environment".[1] An EIS is a tool for decision making. It describes the positive and negative environmental effects of a proposed action, and it usually also lists one or more alternative actions that may be chosen instead of the action described in the EIS. 

Source - https://en.wikipedia.org/wiki/Environmental_impact_statement

## WARC format files

Web ARChive (WARC) archive format specifies a method for combining multiple digital resources into an aggregate archive file together with related information. The WARC format is a revision of the Internet Archive's ARC File Format[5] that has traditionally been used to store "web crawls" as sequences of content blocks harvested from the World Wide Web. The WARC format generalizes the older format to better support the harvesting, access, and exchange needs of archiving organizations. Besides the primary content currently recorded, the revision accommodates related secondary content, such as assigned metadata, abbreviated duplicate detection events, and later-date transformations.

WARC is recognised by most national library systems as the standard to follow for web archival.

Source - https://en.wikipedia.org/wiki/Web_ARChive

## Docker 

**Docker - Build, Ship, and Run Any App, Anywhere**

Docker is an open-source project that automates the deployment of applications inside software containers.
Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.

1. For Developers
  + Language, Stack, Application independent
  + Eliminates dependencies
  + Easy to configure clusters (Built-in container orchestration)
 
2. For Dev Ops
  + Scalability
  + Efficiency
  + Application security
  
### Containers

Unlike VMs, containers do not bundle a full operating system - only libraries and settings required to make the software work are needed. This makes for efficient, lightweight, self-contained systems and guarantees that software will always run the same, regardless of where it’s deployed.

Using containers, everything required to make a piece of software run is packaged into isolated containers.


Source - https://www.docker.com/what-docker
