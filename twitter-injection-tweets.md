# A collection of tweets about injection techniques

## These were all public tweets. However, I have removed attribution because I did not get their permission to reproduce them here.
<br>
<hr>
If you take an existing IMEI and swap out the last 5 digits, the -1/6 digit from the right is usually the only digit you need to mess with to make a new valid IMEI that passes checksum.
<hr>
4321123443211234 43/21 123 is a valid Visa card number.
and so are a ton of others. Sometimes, you get lucky. Like on Amazon!
<hr>
if you search on @shodanhq or @binaryedgeio
 for things like "command not found" or "/sbin/init" it'll always be fun. Unless they filter it or something. Then just scan the internet for it yourself, I know a guy!! ;)
<hr>
0, 0000, 000000, 123456, 12345678, admin, password are literally still used everywhere as passwords on users named 'user' 'root' 'demo' 'training' 'test' and others. its amazing, really.
<hr>
With CGI scripts and Perl, sometimes you can use || [sys commands here] -- to execute your system commands along with the input you put inside of the form you just filled out.
<hr>
Some pretty basic search box/form fun includes:
lol;/usr/bin/id or lol;reboot 
￼<hr>
%%% is a really good one for search boxes and other boxes because it satisfies the minimum character limit. (I use this all the time, it rules hehe)
<hr>
%E2%80%8C is my favorite crazy zero-width character, because you can trick some applications and even operating systems into doing things they shouldn't. Imagine if something thought lol.tx%E2%80%8Ct was lol.txt 
￼<hr>
If you type %0d%0a that is URL hex for a carriage return line feed. If it doesnt work, try %250d%250a or look at the query returned in the address bar - it may have scrubbed it somehow.
<hr>
in a lot of search forms, especially ones that update "immediately", you can just hit space 3 times and it'll produce all results.
<hr>
if you type %00 in the search bar on most sites it'll dump every single result.
<hr>
I've used '%%%' on a few websites in the past (three to satisfy the minimum character limit). Worked like a charm. Funny how wildcards aren't being escaped.
<hr>
Here's an interesting one: paste '�' (replacement character U+FFFD) in the Twitter search box. The suggestions are all emoji combinations. The search typeahead endpoint is different with the new UI. My past tweet goes into a bit of detail w/ the previous.
<hr>
Download the contents of https[:]//twitter[.]com/i/search/typeahead.json?count=100&filters=true&q=%EF%BF%BD&result_type=topics%2Cusers&src=SEARCH_BOX for those commonly used emoji combinations. You'll be very disappointed in what is often used.
<hr>
%% sometimes works too (SQLesque)
<hr>
I think what [they're] getting at is that even if % is a wildcard, results need to have 00 in them for %00 to work like that. %00 is probably being translated to NUL byte, which is end of string. And the length check is maybe before the unescaping.
<hr>
I use something similar a lot of the time:
[^\u0000]
Can be used as a regular expression that matches (almost) every character, including line breaks when "." isn't enough
<hr>
%%% works on all Shopify sites
<hr>

From some google searching I found out it’s called a null byte. There are some applications for its use as an exploit.  [http://projects.webappsec.org/w/page/13246949/Null%20Byte%20Injection][1]

[1]:http://projects.webappsec.org/w/page/13246949/Null%20Byte%20Injection