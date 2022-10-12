# malice-fprot

[![Circle CI](https://circleci.com/gh/malice-plugins/fprot.png?style=shield)](https://circleci.com/gh/malice-plugins/fprot) [![License](http://img.shields.io/:license-mit-blue.svg)](http://doge.mit-license.org) [![Docker Stars](https://img.shields.io/docker/stars/malice/fprot.svg)](https://hub.docker.com/r/malice/fprot/) [![Docker Pulls](https://img.shields.io/docker/pulls/malice/fprot.svg)](https://hub.docker.com/r/malice/fprot/) [![Docker Image](https://img.shields.io/badge/docker%20image-271MB-blue.svg)](https://hub.docker.com/r/malice/fprot/)

Malice F-PROT AntiVirus Plugin

> This repository contains a **Dockerfile** of [fprot](http://www.fprot.net/lang/en/) for [Docker](https://www.docker.io/)'s [trusted build](https://index.docker.io/u/malice/fprot/) published to the public [DockerHub](https://index.docker.io/).

### Dependencies

- [ubuntu:bionic (_84.1 MB_\)](https://hub.docker.com/_/ubuntu/)

## Installation

1. Install [Docker](https://www.docker.io/).
2. Download [trusted build](https://hub.docker.com/r/malice/fprot/) from public [DockerHub](https://hub.docker.com): `docker pull malice/fprot`

## Usage

```
docker run --rm malice/fprot EICAR
```

### Or link your own malware folder:

```bash
$ docker run --rm -v /path/to/malware:/malware:ro malice/fprot FILE

Usage: fprot [OPTIONS] COMMAND [arg...]

Malice F-PROT AntiVirus Plugin

Version: v0.1.0, BuildTime: 20180903

Author:
  blacktop - <https://github.com/blacktop>

Options:
  --verbose, -V          verbose output
  --table, -t            output as Markdown table
  --callback, -c         POST results to Malice webhook [$MALICE_ENDPOINT]
  --proxy, -x            proxy settings for Malice webhook endpoint [$MALICE_PROXY]
  --elasticsearch value  elasticsearch url for Malice to store results [$MALICE_ELASTICSEARCH_URL]
  --timeout value        malice plugin timeout (in seconds) (default: 60) [$MALICE_TIMEOUT]
  --help, -h             show help
  --version, -v          print the version

Commands:
  update  Update virus definitions
  web     Create a F-PROT scan web service
  help    Shows a list of commands or help for one command

Run 'fprot COMMAND --help' for more information on a command.
```

## Sample Output

### [JSON](https://github.com/malice-plugins/fprot/blob/master/docs/results.json)

```json
{
  "f-prot": {
    "infected": true,
    "result": "EICAR_Test_File (exact)",
    "engine": "4.6.5.141",
    "updated": "20170122"
  }
}
```

### [Markdown](https://github.com/malice-plugins/fprot/blob/master/docs/SAMPLE.md)

---

#### F-PROT

| Infected | Result                  | Engine    | Updated  |
| -------- | ----------------------- | --------- | -------- |
| true     | EICAR_Test_File (exact) | 4.6.5.141 | 20170122 |

---

## Documentation

- [To write results to ElasticSearch](https://github.com/malice-plugins/fprot/blob/master/docs/elasticsearch.md)
- [To create a F-PROT scan micro-service](https://github.com/malice-plugins/fprot/blob/master/docs/web.md)
- [To post results to a webhook](https://github.com/malice-plugins/fprot/blob/master/docs/callback.md)
- [To update the AV definitions](https://github.com/malice-plugins/fprot/blob/master/docs/update.md)

## Issues

Find a bug? Want more features? Find something missing in the documentation? Let me know! Please don't hesitate to [file an issue](https://github.com/malice-plugins/fprot/issues/new).

## CHANGELOG

See [`CHANGELOG.md`](https://github.com/malice-plugins/fprot/blob/master/CHANGELOG.md)

## Contributing

[See all contributors on GitHub](https://github.com/malice-plugins/fprot/graphs/contributors).

Please update the [CHANGELOG.md](https://github.com/malice-plugins/fprot/blob/master/CHANGELOG.md) and submit a [Pull Request on GitHub](https://help.github.com/articles/using-pull-requests/).

## License

MIT Copyright (c) 2016 **blacktop**
