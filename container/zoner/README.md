# malice-zoner

[![Circle CI](https://circleci.com/gh/malice-plugins/zoner.png?style=shield)](https://circleci.com/gh/malice-plugins/zoner)
[![License](http://img.shields.io/:license-mit-blue.svg)](http://doge.mit-license.org)
[![Docker Stars](https://img.shields.io/docker/stars/malice/zoner.svg)](https://hub.docker.com/r/malice/zoner/)
[![Docker Pulls](https://img.shields.io/docker/pulls/malice/zoner.svg)](https://hub.docker.com/r/malice/zoner/)
[![Docker Image](https://img.shields.io/badge/docker%20image-144MB-blue.svg)](https://hub.docker.com/r/malice/zoner/)

> Malice [Zoner](http://www.zonerantivirus.com/stahnout) AntiVirus Plugin

---

### Dependencies

- [ubuntu:bionic (_84.1 MB_\)](https://hub.docker.com/_/ubuntu/)

## Installation

1. Install [Docker](https://www.docker.io/).
2. Download [trusted build](https://hub.docker.com/r/malice/zoner/) from public [DockerHub](https://hub.docker.com): `docker pull malice/zoner`

## Usage

```
docker run --rm malice/zoner EICAR
```

### Or link your own malware folder:

```bash
$ docker run --rm -v /path/to/malware:/malware:ro malice/zoner FILE

Usage: zoner [OPTIONS] COMMAND [arg...]

Malice Zoner AntiVirus Plugin

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
  web     Create a Zoner scan web service
  help    Shows a list of commands or help for one command

Run 'zoner COMMAND --help' for more information on a command.
```

This will output to stdout and POST to malice results API webhook endpoint.

## Sample Output

### [JSON](https://github.com/malice-plugins/zoner/blob/master/docs/results.json)

```json
{
  "zoner": {
    "infected": true,
    "result": "EICAR.Test.File-NoVirus",
    "engine": "1979756",
    "updated": "20170707"
  }
}
```

### [Markdown](https://github.com/malice-plugins/zoner/blob/master/docs/SAMPLE.md)

---

#### Zoner

| Infected |         Result          | Engine  | Updated  |
| :------: | :---------------------: | :-----: | :------: |
|   true   | EICAR.Test.File-NoVirus | 1979756 | 20170707 |

---

## Documentation

- [To write results to ElasticSearch](https://github.com/malice-plugins/zoner/blob/master/docs/elasticsearch.md)
- [To create a Zoner scan micro-service](https://github.com/malice-plugins/zoner/blob/master/docs/web.md)
- [To post results to a webhook](https://github.com/malice-plugins/zoner/blob/master/docs/callback.md)
- [To update the AV definitions](https://github.com/malice-plugins/zoner/blob/master/docs/update.md)

## Issues

Find a bug? Want more features? Find something missing in the documentation? Let me know! Please don't hesitate to [file an issue](https://github.com/malice-plugins/zoner/issues/new).

## CHANGELOG

See [`CHANGELOG.md`](https://github.com/malice-plugins/zoner/blob/master/CHANGELOG.md)

## Contributing

[See all contributors on GitHub](https://github.com/malice-plugins/zoner/graphs/contributors).

Please update the [CHANGELOG.md](https://github.com/malice-plugins/zoner/blob/master/CHANGELOG.md) and submit a [Pull Request on GitHub](https://help.github.com/articles/using-pull-requests/).

## License

MIT Copyright (c) 2016 **blacktop**
