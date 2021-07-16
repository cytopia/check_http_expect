# check_http_expect

Nagios plugin that will check a website (behind .htacess and/or behind POST login) for an expected string based on a simple string or regex expression.

[![Build Status](https://travis-ci.org/cytopia/check_http_expect.svg?branch=master)](https://travis-ci.org/cytopia/check_http_expect)
[![Latest Stable Version](https://poser.pugx.org/cytopia/check_http_expect/v/stable)](https://packagist.org/packages/cytopia/check_http_expect) [![Total Downloads](https://poser.pugx.org/cytopia/check_http_expect/downloads)](https://packagist.org/packages/cytopia/check_http_expect) [![Latest Unstable Version](https://poser.pugx.org/cytopia/check_http_expect/v/unstable)](https://packagist.org/packages/cytopia/check_http_expect) [![License](https://poser.pugx.org/cytopia/check_http_expect/license)](http://opensource.org/licenses/MIT)
[![POSIX](https://img.shields.io/badge/posix-100%25-brightgreen.svg)](https://en.wikipedia.org/?title=POSIX)
[![Type](https://img.shields.io/badge/type-%2Fbin%2Fsh-red.svg)](https://en.wikipedia.org/?title=Bourne_shell)


---

| [![Awesome-Nagios-Plugins](https://raw.githubusercontent.com/cytopia/awesome-nagios-plugins/master/doc/img/awesome-nagios.png)](https://github.com/cytopia/awesome-nagios-plugins) | Find more plugins at [Awesome Nagios](https://github.com/cytopia/awesome-nagios-plugins) |
|---|---|
| [![Icinga Exchange](https://raw.githubusercontent.com/cytopia/awesome-nagios-plugins/master/doc/img/icinga.png)](https://exchange.icinga.com/cytopia) | **Find more plugins at [Icinga Exchange](https://exchange.icinga.com/cytopia)** |
| [![Nagios Exchange](https://raw.githubusercontent.com/cytopia/awesome-nagios-plugins/master/doc/img/nagios.png)](https://exchange.nagios.org/directory/Owner/cytopia/1) | **Find more plugins at [Nagios Exchange](https://exchange.nagios.org/directory/Owner/cytopia/1)** |

---

## 1. Usage

```shell
Usage:  check_http_expect --url <url> --find <string> [--find <another string>] [--huser <user>] [--hpass <pass>] [--lurl <url>] [--cookie <cookie-string> [--cookie <cookie-string>]] [--ldata <data> [--ldata <data>]]

  --url         Target URL
  --find        Find string in source of Target URL ('grep -E'-style regex allowed / can be specified multiple times)
  --huser       (Optional) htaccess username
  --hpass       (Optional) htaccess password
  --lurl        (Optional) Url for POST login
  --ldata       (Optional) POST data (can be specified multiple times)
  --cookie      (Optional) set cookies for request
  --ua          (Optional) use this user-agent instead of the default one
```

## 2. Generic Examples

### 2.1 Search simple string

Check if a website contains the word `google`
```shell
$ check_http_expect --url https://google.com --find google
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

### 2.2 Search via regex

Check if a website contains the the following regex `[0-9]+`
```shell
$ check_http_expect --url https://google.com --find '[0-9]+'
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

### 2.3 .htaccess protection

Check behind a .htaccess protected website for the string `Your site is secured`
```
$ check_http_expect --url "http://www.example.com" --find 'Your site is secured' --huser john --hpass "Password"
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

### 2.4 POST Login

Login to at `http://www.example.com/login.php` with POST data `usernameFieldName=John`, `passwordFieldName=pass`, `submit=1`, go to `http://www.example.com` and check for the regex `[0-9]+`
```
$ check_http_expect --url "http://www.example.com" --find '[0-9]+' --lurl "http://www.example.com/login.php" --ldata  "usernameFieldName=John" --ldata "passwordFieldName=pass" --ldata "submit=1"
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

### 2.5 Set Cookies
Send a request with a custom cookie
```shell
$ check_http_expect --url https://google.com --find google --cookie "username=test"
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

### 2.6 Multiple Search-Strings and different user-agent
When searching for multiple strings on the same page, its not necessary to do multiple requests. 
Just specify as many `find`-parameters as you need.
You may want to change the user-agent with the parameter `ua` as well, in order to avoid undesired redirects.
```shell
$ check_hmg_http_expect --url "https://duckduckgo.com" --find "<title>DuckDuckGo — Privacy, simplified.</title>" --find "<span class=\"logo_homepage__tt\">Duck it\!</span>" --find "NO-MATCH" --ua "curl/7.37.0"
[WARN] Not all matches found for: "<title>DuckDuckGo — Privacy, simplified.</title>
<span class="logo_homepage__tt">Duck it\!</span>
NO-MATCH" | 'Results'=2 but expected: 3
Http version:  HTTP/2
Http code:     200
Http info:     
Server:        
Url:           https://duckduckgo.com
Search:        <title>DuckDuckGo — Privacy, simplified.</title> 
               <span class="logo_homepage__tt">Duck it\!</span> 
               NO-MATCH
               
Num matches:   2
Matches:
----------------------------------------
        <title>DuckDuckGo — Privacy, simplified.</title>                                <span class="logo_homepage__tt">Duck it!</span>
```
If only some of the specified search-strings are matched, then a warning is returned. 

## 3. Specific Examples

### 3.1 Log into wordpress.com

Find String in Wordpress.com dashboard

```shell
$ check_http_expect --url "https://dashboard.wordpress.com/wp-admin/" --find "Recently Published" --lurl "https://wordpress.com/wp-login.php" --ldata "log=USER@EMAIL" --ldata "pwd=PASSWORD" --ldata "rememberme=forever" --ldata "testcookie=1"
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


## 4. Awesome

Added by the following [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome) lists:

* [awesome-nagios-plugins](https://github.com/cytopia/awesome-nagios-plugins)
