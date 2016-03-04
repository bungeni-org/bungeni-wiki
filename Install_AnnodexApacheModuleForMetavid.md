### Introduction

Ubuntu packages of mod\_annodex for newer versions of Ubuntu are no longer published, this page describes how to build the module from source. If you are using Ubuntu dapper, however, the mod\_annodex package is available in the repository


### Download

Download the source package from

`http://www.annodex.net/software/mod_annodex/download/mod_annodex-ap20-0.2.2.tar.gz`

### Install Prerequisites

Assumes you already have Apache2(mpm) installed

```
sudo apt-get install libannodex-dev libcmml-dev libogg-dev libapr-dev apache2-threaded-dev
```

### Build and Install

```
tar xvf mod_annodex-ap20-0.2.2.tar.gz
cd mod_annodex-ap20-0.2.2
apxs2 -c mod_annodex.c -lannodex -ldl -loggz -logg -lcmml -lexpat -lm
sudo apxs2 -i mod_annodex.la
sudo cp annodex.load /etc/apache/mod_available/
sudo cp annodex.conf /etc/apache/mod_available/ 
```