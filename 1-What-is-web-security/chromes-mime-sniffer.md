# Selected (most) comments from Chrome's mime_sniffer.cc

## See source here: [https://source.chromium.org/chromium/chromium/src/+/master:net/base/mime_sniffer.cc;l=5?originalUrl=https:%2F%2Fcs.chromium.org%2Fchromium%2Fsrc%2Fnet%2Fbase%2Fmime_sniffer.cc][1]

## There are lots of magic numbers in that source code.  Here's a long list of file signatures (i.e., their magic numbers): [https://en.wikipedia.org/wiki/List_of_file_signatures][2] 

## Here's a fun link to some mostly unrelated magic numbers: [https://www.howtogeek.com/201059/magic-numbers-the-secret-codes-that-programmers-hide-in-your-pc/][3]

## Relatedly, if you're on Mac OS X you can pretty-print the first 128 bytes in hex of a file with this command: `hexdump -C -n 128 <path to file>`

## Here's another fun link: the MIME Sniffing living standard: [https://mimesniff.spec.whatwg.org/][4]

## The selected comments:
Detecting mime types is a tricky business because we need to balance  
compatibility concerns with security issues.  Here is a survey of how other  
browsers behave and then a description of how we intend to behave.

HTML payload, no Content-Type header:
* IE 7: Render as HTML
* Firefox 2: Render as HTML
* Safari 3: Render as HTML
* Opera 9: Render as HTML

Here the choice seems clear:  
=> Chrome: Render as HTML  

HTML payload, Content-Type: "text/plain":
* IE 7: Render as HTML
* Firefox 2: Render as text
* Safari 3: Render as text (Note: Safari will Render as HTML if the URL
                                  has an HTML extension)
* Opera 9: Render as text

Here we choose to follow the majority (and break some compatibility with IE).  
Many folks dislike IE's behavior here.  
=> Chrome: Render as text  
We generalize this as follows.  If the Content-Type header is text/plain  
we won't detect dangerous mime types (those that can execute script).  

HTML payload, Content-Type: "application/octet-stream":
* IE 7: Render as HTML
* Firefox 2: Download as application/octet-stream
* Safari 3: Render as HTML
* Opera 9: Render as HTML

We follow Firefox.  
=> Chrome: Download as application/octet-stream  
One factor in this decision is that IIS 4 and 5 will send  
application/octet-stream for .xhtml files (because they don't recognize  
the extension).  We did some experiments and it looks like this doesn't occur  
very often on the web.  We choose the more secure option.  

GIF payload, no Content-Type header:
* IE 7: Render as GIF
* Firefox 2: Render as GIF
* Safari 3: Download as Unknown (Note: Safari will Render as GIF if the
                                       URL has an GIF extension)
* Opera 9: Render as GIF

The choice is clear.  
=> Chrome: Render as GIF  
Once we decide to render HTML without a Content-Type header, there isn't much  
reason not to render GIFs.  

GIF payload, Content-Type: "text/plain":
* IE 7: Render as GIF
* Firefox 2: Download as application/octet-stream (Note: Firefox will
                             Download as GIF if the URL has an GIF extension)
* Safari 3: Download as Unknown (Note: Safari will Render as GIF if the
                                       URL has an GIF extension)
* Opera 9: Render as GIF

Displaying as text/plain makes little sense as the content will look like  
gibberish.  Here, we could change our minds and download.  
=> Chrome: Render as GIF  

GIF payload, Content-Type: "application/octet-stream":
* IE 7: Render as GIF
* Firefox 2: Download as application/octet-stream (Note: Firefox will
                             Download as GIF if the URL has an GIF extension)
* Safari 3: Download as Unknown (Note: Safari will Render as GIF if the
                                       URL has an GIF extension)
* Opera 9: Render as GIF

We used to render as GIF here, but the problem is that some sites want to  
trigger downloads by sending application/octet-stream (even though they  
should be sending Content-Disposition: attachment).  Although it is safe  
to render as GIF from a security perspective, we actually get better  
compatibility if we don't sniff from application/octet stream at all.  
=> Chrome: Download as application/octet-stream  

Note that our definition of HTML payload is much stricter than IE's  
definition and roughly the same as Firefox's definition.  

Sniffing for Flash:  

MAGIC_NUMBER("application/x-shockwave-flash", "CWS")  
MAGIC_NUMBER("application/x-shockwave-flash", "FLV")  
MAGIC_NUMBER("application/x-shockwave-flash", "FWS")  

Including these magic number for Flash is a trade off.  

Pros:  
* Flash is an important and popular file format

Cons:  
* These patterns are fairly weak
* If we mistakenly decide something is Flash, we will execute it
    in the origin of an unsuspecting site.  This could be a security
    vulnerability if the site allows users to upload content.

On balance, we do not include these patterns.    

This function checks for files that have a Microsoft Office MIME type  
set, but are not actually Office files.  

If this is not actually an Office file, |*result| is set to  
"application/octet-stream", otherwise it is not modified.  

Returns false if additional data is required to determine the file type, or  
true if there is enough data to make a decision.  

Returns true and sets result if the content appears to contain XHTML or a  
feed.  
Clears have_enough_content if more data could possibly change the result.  

TODO(evanm): this is similar but more conservative than what Safari does,  
while HTML5 has a different recommendation -- what should we do?  
TODO(evanm): this is incorrect for documents whose encoding isn't a superset  
of ASCII -- do we care?  

We allow at most 300 bytes of content before we expect the opening tag.  

This loop iterates through tag-looking offsets in the file.  
We want to skip XML processing instructions (of the form "<?xml ...")  
and stop at the first "plain" tag, then make a decision on the mime-type  
based on the name (or possibly attributes) of that tag.  

TODO(evanm): handle RSS 1.0, which is an RDF format and more difficult  
to identify.  

Returns true and sets result to "application/octet-stream" if the content  
appears to be binary data. Otherwise, returns false and sets "text/plain".  
Clears have_enough_content if more data could possibly change the result.  

There is no concensus about exactly how to sniff for binary content.  
* IE 7: Don't sniff for binary looking bytes, but trust the file extension.  
* Firefox 3.5: Sniff first 4096 bytes for a binary looking byte.  
Here, we side with FF, but with a smaller buffer. This size was chosen  
because it is small enough to comfortably fit into a single packet (after  
allowing for headers) and yet large enough to account for binary formats  
that have a significant amount of ASCII at the beginning (crbug.com/15314).   

First, we look for a BOM.  

If there is BOM, we think the buffer is not binary.  

Next we look to see if any of the bytes "look binary."  

No evidence either way. Default to non-binary and, if truncated, clear  
have_enough_content because there could be a binary looking byte in the  
truncated data.  

Empty mime types are as unknown as they get.  
""  
The unknown/unknown type is popular and uninformative  
"unknown/unknown"  
The second most popular unknown mime type is application/unknown  
"application/unknown"  
Firefox rejects a mime type if it is exactly */*  
"*/*"  

Firefox rejects a mime type if it does not contain a slash  

Returns true and sets result if the content appears to be a crx (Chrome  
extension) file.  
Clears have_enough_content if more data could possibly change the result.  

Technically, the crx magic number is just Cr24, but the bytes after that  
are a version number which changes infrequently. Including it in the  
sniffing gives us less room for error. If the version number ever changes,  
we can just add an entry to this list.  

MAGIC_NUMBER("application/x-chrome-extension", "Cr24\x02\x00\x00\x00")  
MAGIC_NUMBER("application/x-chrome-extension", "Cr24\x03\x00\x00\x00")  

Only consider files that have the extension ".crx".  

Many web servers are misconfigured to send text/plain for many  
different types of content.  
"text/plain"  
We want to sniff application/octet-stream for  
application/x-chrome-extension, but nothing else.  
"application/octet-stream"  
XHTML and Atom/RSS feeds are often served as plain xml instead of  
their more specific mime types.  
"text/xml"  
"application/xml"  
Check for false Microsoft Office MIME types.  
"application/msword"  
"application/vnd.ms-excel"  
"application/vnd.ms-powerpoint"  
"application/vnd.openxmlformats-officedocument.wordprocessingml.document"  
"application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"  
"application/vnd.openxmlformats-officedocument.presentationml.presentation"  
"application/vnd.ms-excel.sheet.macroenabled.12"  
"application/vnd.ms-word.document.macroenabled.12"  
"application/vnd.ms-powerpoint.presentation.macroenabled.12"  
"application/mspowerpoint"  
"application/msexcel"  
"application/vnd.ms-word"  
"application/vnd.ms-word.document.12"  
"application/vnd.msword"  

By default, we assume we have enough content.  
Each sniff routine may unset this if it wasn't provided enough content.  

By default, we'll return the type hint.  
Each sniff routine may modify this if it has a better guess..  

If the file has a Microsoft Office MIME type, we should only check that it  
is a valid Office file.  Because this is the only reason we sniff files  
with a Microsoft Office MIME type, we can return early.  

First check for HTML, unless it's a file URL and  
|allow_sniffing_files_urls_as_html| is false.  

We're only willing to sniff HTML if the server has not supplied a mime  
type, or if the type it did supply indicates that it doesn't know what  
the type should be.  

We're only willing to sniff for binary in 3 cases:  
1. The server has not supplied a mime type.ß
2. The type it did supply indicates that it doesn't know what the type
   should be.
3. The type is "text/plain" which is the default on some web servers and
   could be indicative of a mis-configuration that we shield the user from.

If the server said the content was text/plain and it doesn't appear  
    to be binary, then we trust it.  

If we have plain XML, sniff XML subtypes.  

We're not interested in sniffing these types for images and the like.  
Instead, we're looking explicitly for a feed.  If we don't find one  
we're done and return early.  

CRX files (Chrome extensions) have a special sniffing algorithm. It is  
tighter than the others because we don't have to match legacy behavior.  

Check the file extension and magic numbers to see if this is an Office  
document.  This needs to be checked before the general magic numbers  
because zip files and Office documents (OOXML) have the same magic number.  

We're not interested in sniffing for magic numbers when the type_hint  
is application/octet-stream.  Time to bail out.  

Now we look in our large table of magic numbers to see if we can find  
anything that matches the content.  

The definition of "binary bytes" is from the spec at  
https://mimesniff.spec.whatwg.org/#binary-data-byte  

The bytes which are considered to be "binary" are all < 0x20. Encode them  
one bit per byte, with 1 for a "binary" bit, and 0 for a "text" bit. The  
least-significant bit represents byte 0x00, the most-significant bit  
represents byte 0x1F.  



[1]: https://source.chromium.org/chromium/chromium/src/+/master:net/base/mime_sniffer.cc;l=5?originalUrl=https:%2F%2Fcs.chromium.org%2Fchromium%2Fsrc%2Fnet%2Fbase%2Fmime_sniffer.cc
[2]: https://en.wikipedia.org/wiki/List_of_file_signatures
[3]: https://www.howtogeek.com/201059/magic-numbers-the-secret-codes-that-programmers-hide-in-your-pc/
[4]: https://mimesniff.spec.whatwg.org/