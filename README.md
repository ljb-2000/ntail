ntail
=====

A `tail(1)`-like utility for nginx log files that supports parsing, filtering and formatting of individual 
log lines (in nginx's so-called ["combined" log format](http://wiki.nginx.org/NginxHttpLogModule#log_format)).

Installation
------------

Installing the gem also installs the `ntail` executable, typically as `/usr/bin/ntail` or `/usr/local/bin/ntail`:

    $ gem install ntail

To ensure easy execution of the `ntail` script, add the actual installation directory to your shell's `$PATH` variable.

Basic Usage
-----------

* process an entire nginx log file and print each parsed and formatted line to STDOUT

        $ ntail /var/log/nginx/access.log

* tail an "active" nginx log file and print each new line to STDOUT _(stop with ^C)_

        $ tail -f /var/log/nginx/access.log | ntail

Advanced Examples
-----------------

* read from STDIN and print each line to STDOUT _(stop with ^D)_

        $ ntail

* read from STDIN and print out the length of each line _(to illustrate -e option)_

        $ ntail -e '{ |line| puts line.size }'

* read from STDIN but only print out non-empty lines _(to illustrate -f option)_

        $ ntail -f '{ |line| line.size $ 0 }'

* the following invocations behave exactly the same _(to illustrate -e and -f options)_

        $ ntail
        $ ntail -f '{ |line| true }' -e '{ |line| puts line }'

* print out all HTTP requests that are coming from a given IP address

        $ ntail -f '{ |line| line.remote_address == "208.67.222.222" }' /var/log/nginx/access.log

* find all HTTP requests that resulted in a '5xx' HTTP error/status code _(e.g. Rails 500 errors)_

        $ gunzip -S .gz -c access.log-20101216.gz | ntail -f '{ |line| line.server_error_status? }'

* generate a summary report of HTTP status codes, for all non-200 HTTP requests

        $ ntail -f '{ |line| line.status != "200" }' -e '{ |line| puts line.status }' access.log | sort | uniq -c
        76 301
        16 302
         2 304
         1 406

* print out GeoIP country and city information for each HTTP request _(depends on the optional `geoip` gem)_

        $ ntail -e '{ |line| puts [line.to_country_s, line.to_city_s].join("\t") }' /var/log/nginx/access.log
        United States   Los Angeles
        United States   Houston
        Germany         Berlin
        United Kingdom  London

* print out the IP address and the corresponding host name for each HTTP request _(slows things down considerably, due to `nslookup` call)_

        $ ntail -e '{ |line| puts [line.remote_address, line.to_host_name].join("\t") }' /var/log/nginx/access.log
        66.249.72.196   crawl-66-249-72-196.googlebot.com
        67.192.120.134  s402.pingdom.com
        75.31.109.144   adsl-75-31-109-144.dsl.irvnca.sbcglobal.net
    
TODO
----
    
* implement a native `"-f"` option for ntail, similar to that of `tail(1)`, using e.g. flori's [file-tail gem](https://github.com/flori/file-tail)
* implement a `"-i"` option ("ignore exceptions"/"continue processing"), if handling a single line raises an exception
* or indeed a reverse `"-r"` option ("re-raise exception"), to immediately stop processing and raising the exception for investigation
* implement (better) support for custom nginx log formats, in addition to [nginx's default "combined" log format](http://wiki.nginx.org/NginxHttpLogModule#log_format).

Acknowledgements
----------------

* ntail's parsing feature is inspired by an nginx log parser written by [Richard Taylor (moomerman)](https://github.com/moomerman)
* parsing and expanding ntail's formatting string is done using nathansobo's quite brilliant [treetop gem](https://github.com/nathansobo/treetop)

Contributing to ntail
---------------------
 
* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it in a future version unintentionally.
* Please try not to mess with the Rakefile, version, or history. If you want to have your own version, or is otherwise necessary, that is fine, but please isolate to its own commit so I can cherry-pick around it.

Copyright
---------

Copyright (c) 2011 Peter Vandenberk. See LICENSE.txt for further details.
