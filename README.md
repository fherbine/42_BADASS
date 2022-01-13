Bgp At Doors of Autonomous Systems is Simple (BADASS)
========

This is a 42 project about BGP.

# Introduction

This project is designed for us to learn the role/use of BGP it introduce us to
GNS3 software using our own docker images.

# Part 1

## How to run

First, you will need to build your images with the following commands:
```sh
$ cd P1
$ sudo docker build -t host_fherbine -f ./_fherbine_host-1 .
$ sudo docker build -t routeur_aabelque -f ./_aabelque_routeur-1 .
```

## Glossary

AS = Autonomous System
BGP = Border Gateway Protocol
