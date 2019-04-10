
---
title: “Getting my site security from D to A+ (without spending a dime)”
date: 2018-11-06T02:30:07-07:00
draft: true
timeToRead: “10 min”
---

Working on the security industry and having an insecure site looks super bad. 

So imagine how I felt after discovering that my brand new personal site sucked:

> SOME IMAGE 

To fix this, I decided to improve my security score.

I’m sharing my findings and solutions here so you can improve your site security too.

## Assessment... How bad I was

*TIP:* If you’re just into the solution, skip this ;)

For starters, I decided to check my personal site (sudobinbash.com) security.

My first impression was that my site security was fine. I don’t host anything fancy, I have HTTPS, I checked which libraries/packages I use, and I took the easiest path towards risk avoidance:

- 100% static built (plain vanilla Markdowns).
- No Wordpress or a CMSless architecture. 
- No input forms.

This approach is easy to take when you’re just hosting a personal portfolio or blog (not for a full business).

To host, build, and serve my site, I use:

- **CloudFlare** for CDN and free HTTPS.
- **Github** for hosting page source.
- **Hugo** for building my static site. 

To check my security. I used:

- securityheaders.com: To check my site headers
- Qualys: To check my site cryptography

And the results were quite disappointing. What I discovered:

1. I didn’t have a single security header.
2. My DNSSEC was off.
3. I was supporting legacy SSL and not using the latest TLS implementations.

So I had to do something about it.

## Security fine tuning in CloudFlare

My first idea was to fix everything in Cloudflare. With Cloudflare, I was able to:

- **Configure DNSSEC**: <How-to here>

- **Configure HTTPS only**: <How-to here>

- **Use only TLS v1.2**:  <How-to here>

Cloudflare fixed all the issues pointed by Qualys and improved my header security score to a C-.

That’s awesome, but not enough...

## Netlify to the rescue

For security headers, I was thinking about using Cloudflare Workers – an awesome serverless implementation that run functions straight from your CDN, reducing latency – but Cloudflare started charging $5/month for it, so I had to go after something else.

For that, I decided to switch from github pages to **Netlify**.

Netlify is a static host built for developers with native GitHub integration (it updates your website automatically everytime you merge something).

I used Netlify in the past for a project that didn’t pan out, but I really got impressed with their capabilities. Their premium tier supports a lot of cool things, like generating staging websites from git branches, providing contact forms, etc.

To “migrate” from github pages to Netlify, I used this get started tutorial provided by Hugo. <LINK>

After that, I just had to create a netlify config file `netlify.toml` and work on my site headers (following the recommendations from securityheaders.com):

```
[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    Content-Security-Policy = "form-action https:"
    Referrer-Policy = "strict-origin-when-cross-origin"
    Strict-Transport-Security = "max-age=2592000"
    Feature-Policy = "vibrate 'none'; geolocation 'none'; midi 'none'; notifications 'none'; push 'none'; sync-xhr 'none'; microphone 'none'; camera 'none'; magnetometer 'none'; gyroscope 'none'; speaker 'none'; vibrate 'none'; fullscreen 'none'; payment 'none'"
```

### Checking the results

After all the configuration:

1: I didn’t have a single security header problem. (thanks Netlify)
2: My DNSSEC was off. (thanks CloudFlare)
3: I was supporting legacy crypto and not using the latest TLS implementations. (thanks CloudFlare)

The coolest part, is I could do everything for free and I learned a lot more about web security.