# Virus Scanner Microservices

This repository contains a set of docker container orchestrated via docker-compose. Each container is an antivirus microservice accepting files to scan over HTTP. I used these microservices for my [Short story about evading Antivirus Detection](https://thecout.com/blog/virscan/) blog post.

All containers are based off the plugins from the discontinued/unmantained https://github.com/maliceio/ project. As far as I could, I fixed all services, however most containers run on obsolete Ubuntu and Go versions. 
I hope to bump these versions some day, but they should work for now.

## Install

To use MaliceIOs original images from the docker-hub use:

```
docker-compose up -d
```

You are best advice to build the microservices to get the newest signatures

```
docker-compose -f docker-compose-build.yml up --build -d
```

## Usage

E.g. to use windows-defender (with httpie):

```
http -f localhost:3993/scan malware@/path/to/evil/malware
```

Response:

```
HTTP/1.1 200 OK
Content-Length: 124
Content-Type: application/json; charset=UTF-8
Date: Sat, 21 Jan 2017 05:39:29 GMT

{
  "windows-defender": {
    "infected": true,
    "result": "Virus:DOS/EICAR_Test_File",
    "engine": "0.1.0",
    "updated": "20170527"
  }
}
```


## Services

| Service          | Name            | HTTP Port | Notes                          |
|------------------|-----------------|-----------|--------------------------------|
| Windows-Defender | windowsdefender | 3993      |                                |
| Mcafee           | mcafee          | 3994      |                                |
| Comodo           | comodo          | 3995      |                                |
| Bitdefender      | bitdefender     | 3996      | Only 30 days trial             |
| Sophos           | sophos          | 3997      |                                |
| Zoner            | zoner           | 3998      |                                |
| ClamAV           | clamav          | 3999      |                                |
| eScan            | escan           | 4000      |                                |
| Dr. Web          | drweb           | 4001      |                                |
| F-Secure         | fsecure         | 4002      |                                |
| Kaspersky        | kaspersky       | 4003      |                                |
| AVG              | avg             | 4004      |                                |
| YARA             | yara            | 4005      |                                |
| F-Prot           | fprot           | 4006      |                                |
| PEScan           | pescan          | 4007      | Outputs Certain PE Information |
| FileInfo         | fileinfo        | 4008      | Outputs File Info              |
