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

## Examples

Check if a website contains the word `google`
```shell
$ check_http_expect --url=https://google.com --find=google
[OK] 1 match found for: "google".
Http version:  HTTP/1.1
Http code:     302
Http info:     Found
Server:        GFE/2.0
Url:           https://google.com
Search:        google
Num matches:   1
Matches:
----------------------------------------
<A HREF=https://www.google.de/?gfe_rd=cr&amp;ei=B9AxVr7RJerj8weKoa2IBA>here</A>
```

Check if a website contains the the following regex `[0-9]+`
```shell
$ check_http_expect --url=https://google.com --find='[0-9]+'
[OK] 4 matches found for: "[0-9]+".
Http version:  HTTP/1.1
Http code:     302
Http info:     Found
Server:        GFE/2.0
Url:           https://google.com
Search:        [0-9]+
Num matches:   4
Matches:
----------------------------------------
<HTML><HEAD><meta http-equiv=content-type content=text/html;charset=utf-8> <TITLE>302 Moved</TITLE></HEAD><BODY> <H1>302 Moved</H1> <A HREF=https://www.google.de/?gfe_rd=cr&amp;ei=Q9AxVs-AAurj8weKoa2IBA>here</A>.
```

Check behind a .htaccess protected website for the string `Your site is secured`
```
$ check_http_expect --url="http://www.example.com" --find='Your site is secured' --huser=john --hpass="Password"
[ERROR] No matches found for: "Your site is secured".
Http version:  HTTP/1.1
Http code:     302
Http info:     Found
Server:        Apache/2.4.16 (Amazon) PHP/5.5.30
Url:           http://www.example.com
Search:        Your site is secured
Num matches:   0
Matches:
----------------------------------------
```

Login to at `http://www.example.com/login.php` with POST data `usernameFieldName=John`, `passwordFieldName=pass`, `submit=1`, go to `http://www.example.com` and check for the regex `[0-9]+`
```
$ check_http_expect --url="http://www.example.com" --find='[0-9]+' --lurl="http://www.example.com/login.php" --ldata="usernameFieldName=John" --ldata="passwordFieldName=pass" --ldata="submit=1"
[ERROR] No matches found for: "[0-9]+".
Http version:  HTTP/1.1
Http code:     302
Http info:     Found
Server:        Apache/2.4.16 (Amazon) PHP/5.5.30
Url:           http://www.example.com
Search:        Your site is secured
Num matches:   0
Matches:
```

**Note:** htaccess and POST login can also be combined.
