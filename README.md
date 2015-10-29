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

## Generic Examples

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


## Specific Examples

### Find String in Wordpress.com dashboard

```shell
$ check_http_expect --url="https://dashboard.wordpress.com/wp-admin/" --find="Recently Published" --lurl="https://wordpress.com/wp-login.php" --ldata="log=USER@EMAIL" --ldata="pwd=PASSWORD" --ldata="rememberme=forever" --ldata="testcookie=1"
[OK] 1 match found for: "Recently Published".
Http version:  HTTP/1.1
Http code:     200
Http info:     OK
Server:        nginx
Url:           https://dashboard.wordpress.com/wp-admin/
Search:        Recently Published
Num matches:   1
Matches:
----------------------------------------
<div id=activity-widget><div id=published-posts class=activity-block><h4>Recently Published</h4><ul><li><span>Feb 20th, 1:41 am</span> <a href=https://dashboard.wordpress.com/2014/02/20/test/>Test</a></li><li><span>Jun 2nd, 6:10 pm</span> <a href=https://dashboard.wordpress.com/2011/06/02/life-would-be-so-different-if-everyone-j/>Life would be so different if everyone j</a></li><li><span>Jun 1st, 10:08 pm</span> <a href=https://dashboard.wordpress.com/2011/06/01/for-those-of-you-who-are-still-paying-re/>for those of you who are still paying re</a></li><li><span>Jun 1st, 9:52 pm</span> <a href=https://dashboard.wordpress.com/2011/06/01/having-a-great-day-so-far-and-i-hope-all/>Having a great day so far and I hope all</a></li><li><span>May 26th, 10:37 pm</span> <a href=https://dashboard.wordpress.com/2011/05/26/in-this-video-you-will-find-out-how-to-r/>In this video you will find out how to r</a></li></ul></div></div></div>
```
