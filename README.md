# check_http_expect

[![Build Status](https://travis-ci.org/cytopia/check_http_expect.svg?branch=master)](https://travis-ci.org/cytopia/check_http_expect)
[![Latest Stable Version](https://poser.pugx.org/cytopia/check_http_expect/v/stable)](https://packagist.org/packages/cytopia/check_http_expect) [![Total Downloads](https://poser.pugx.org/cytopia/check_http_expect/downloads)](https://packagist.org/packages/cytopia/check_http_expect) [![Latest Unstable Version](https://poser.pugx.org/cytopia/check_http_expect/v/unstable)](https://packagist.org/packages/cytopia/check_http_expect) [![License](https://poser.pugx.org/cytopia/check_http_expect/license)](http://opensource.org/licenses/MIT)
[![POSIX](https://img.shields.io/badge/posix-100%25-brightgreen.svg)](https://en.wikipedia.org/?title=POSIX)
[![Type](https://img.shields.io/badge/type-%2Fbin%2Fsh-red.svg)](https://en.wikipedia.org/?title=Bourne_shell)


Nagios plugin that will check a website (behind .htacess and/or behind POST login) for an expected string based on a simple string or regex expression.


## Usage

```shell
Usage:  check_http_expect --url=<url> --find=<string> [--huser=<user>] [--hpass=<pass>] [--lurl=<url>] [--ldata==<data> [--ldata=<data>]]

  --url         Target URL
  --find        Find string in source of Target URL ('grep -E'-style regex allowed)
  --huser       (Optional) htaccess username
  --hpass       (Optional) htaccess password
  --lurl        (Optional) Url for POST login
  --ldata       (Optional) POST data (can be specified multiple times)
```
