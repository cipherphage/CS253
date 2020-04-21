# HTTP and Cookies

## HTTP headers: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers][6]

## Cookies are one way to provide state to a website (HTTP requests are stateless)

## Here's a good overview of cookies (some of the notes below are direct quotes from this): [https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies][1]

## Cookie directives, privacy, and security

Life-span:  
- `Expires` and `Max-Age` tell a browser how long the cookie should live (e.g., a session cookie might live for 30 minutes, even if a browser is closed and re-opened).  
  
Security:  
- `Secure` tells the browser that the cookie should only be attached to a request over HTTPS.  Since Chrome 52 and Firefox 52 websites that aren't on HTTPS can't set cookies with the `Secure` directive.
- `HttpOnly` tells the browser that the `Document.cookie` JavaScript API (which ships with browsers) should not be able to access the cookie. This is a way to prevent the client-side JavaScript from reading/modifying the cookie (e.g., a persistent server-side session cookie) and is therefore a cross-site scripting (XSS) mitigation. 
- To prevent exfiltration of the cookie you should implement strict Content-Security-Policy (see CORB in part 1-What-is-web-security).  This will limit exfiltration avenues even for cookies that don't have the `HttpOnly` directive set.
- `SameSite` tells the browser that the cookie should only be sent on requests on the same registrable domain.  This helps to mitigate cross-site request forgery attacks (CRSF). The directive has three settings:
  - `None` which means `SameSite` is not applied to the cookie.
  - `Strict` which means that `SameSite` is applied as described above (the cookie is only sent on requests on the same registrable domain).
  - `Lax` which is the same as `Strict` EXCEPT for links on external sites, in which case the cookie will be sent.
  - Cookie prefixes are slightly more involved but no less important as they mitigate against session fixation attacks. Check out what Mozilla says about them: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Cookie_prefixes][2]
    - Here's a very short TL;DR about them from that link:
    - Session fixation should primarily be mitigated by regenerating session cookie values when the user authenticates (even if a cookie already exists) and by tieing any CSRF token to the user. As a defence in depth measure, however, it is possible to use <em>cookie prefixes</em> to assert specific facts about the cookie.

Scope:
- `Domain` specifies allowed hosts to receive the cookie. If unspecified, it defaults to the host of the current document location, excluding subdomains. If `Domain` is specified, then subdomains are always included.
- `Path` indicates a URL path that must exist in the requested URL in order to send the Cookie header. The %x2F ("/") character is considered a directory separator, and subdirectories will match as well.

Privacy:
- There are a lot of privacy issues related to 3rd-party tracking of users across the internet. Right now, aside from legal remedies under the European Union's GDPR or EU member states' cookie directives, there is little the average user can do except to try to prevent tracking in the browsers they use by customizing their browser settings, using more privacy/security focused browsers, using privacy/security focused browser extensions, or switching to more extensive solutions such as Tor browser, the Tales operating system, linux phones, etc.
- Do Not Track: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Do-Not-Track][3]
- EU cookie directive, Directive 2009/136/EC: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#EU_cookie_directive][4]
- Beware of Zombie cookies and Evercookies: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Zombie_cookies_and_Evercookies][5]


[1]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
[2]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Cookie_prefixes
[3]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Do-Not-Track
[4]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#EU_cookie_directive
[5]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Zombie_cookies_and_Evercookies
[6]:https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers