# XSS and Related Notes

## Cross Origin Read Blocking: Some recommendations from the Chrome team

<hr>

### See full list of recommendations here: [https://www.chromium.org/Home/chromium-security/corb-for-developers][1]

### For HTML, JSON, and XML resources:

Make sure these resources are served with a correct `Content-Type` response header from the list below, as well as a `X-Content-Type-Options: nosniff` response header.  These headers ensure Chrome can identify the resources as needing protection, without depending on the contents of the resources.  

HTML MIME type - `text/html`  
XML MIME type - `text/xml`, `application/xml`, or any MIME type whose subtype ends in `+xml`  
JSON MIME type - `text/json`, `application/json`, or any MIME type whose subtype ends in `+json`  

Note that we recommend not supporting multipart range requests for sensitive resources, because this changes the MIME type to `multipart/byteranges` and makes it harder for Chrome to protect.  Typical range requests are not a problem and are treated similarly to the `nosniff` case.

In addition to the recommended cases above, Chrome will also do its best to protect responses labeled with any of the MIME types above and without a `nosniff` header, but this has limitations. Many JavaScript files on the web are unfortunately labeled using some of these MIME types, and if Chrome blocked access to them, existing websites would break. Thus, when the `nosniff` header is not present, Chrome first looks at the start of the file to try to confirm whether it is HTML, XML, or JSON, before deciding whether to protect it.  If it cannot confirm this, it allows the response to be received by the cross-site page's process. This is a best-effort approach which adds some limited protection while preserving compatibility with existing sites.  We recommend that web developers include the `nosniff` header to protect their resources, to avoid relying on this "confirmation sniffing" approach.  

NOTE: Firefox just recently added support for `X-Content-Type-Options:nosniff` on page loads: [https://blog.mozilla.org/security/2020/04/07/firefox-75-will-respect-nosniff-for-page-loads/][7]

### For other resource types (e.g., PDF, ZIP, PNG):

Make sure these resources are served only in response to requests that include an unguessable CSRF token (which should be distributed via resources protected via the HTML, JSON, or XML steps above).

If you suspect Chrome is incorrectly blocking a response and that this is disrupting the behavior of a website, please file a Chromium bug describing the incorrectly blocked response (both the headers and body) and/or the URL serving it.  You can confirm if a problem is due to CORB by temporarily disabling it, by starting Chrome with the following command line flag:
`--disable-features=CrossSiteDocumentBlockingAlways,CrossSiteDocumentBlockingIfIsolating`

<hr>  

## Content Security Policy

<hr>  

### These are a collection of server headers that tell the browser how to restict content for your website.  See the walkthrough here: [https://developers.google.com/web/fundamentals/security/csp][2]

### The TL;DR from their walkthrough:
* Use whitelists to tell the client what's allowed and what isn't.
* Learn what directives are available.
* Learn the keywords they take.
* Inline code and eval() are considered harmful.
* Report policy violations to your server before enforcing them.

### Other helpful CSP resources:
* [https://content-security-policy.com/][3]
* [https://scotthelme.co.uk/csp-cheat-sheet/][4]
* [https://github.blog/2016-04-12-githubs-csp-journey/][5]

<hr>

## Feature Policy

<hr>

### These are a collection of server headers that restrict the features for your website. See the walkthrough here: [https://developers.google.com/web/updates/2018/06/feature-policy][6]

### Some examples:
* Change the default behavior of `autoplay` on mobile and third party videos.
* Restrict a site from using sensitive APIs like `camera` or `microphone`.
* Allow iframes to use the `fullscreen` API.
* Block the use of outdated APIs like synchronous XHR and `document.write()`.
* Ensure images are sized properly (e.g. prevent layout thrashing) and are not too big for the viewport (e.g. waste user's bandwidth).


[1]:https://www.chromium.org/Home/chromium-security/corb-for-developers
[2]:https://developers.google.com/web/fundamentals/security/csp
[3]:https://content-security-policy.com/
[4]:https://scotthelme.co.uk/csp-cheat-sheet/
[5]:https://github.blog/2016-04-12-githubs-csp-journey/
[6]:https://developers.google.com/web/updates/2018/06/feature-policy
[7]:https://blog.mozilla.org/security/2020/04/07/firefox-75-will-respect-nosniff-for-page-loads/