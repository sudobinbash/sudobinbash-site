---
title: "Getting my site security from D to A+ (without spending one cent)"
date: 2018-11-06T02:30:07-07:00
draft: true
timeToRead: "10 min"
---

Working on the security industry and having an insecure site looks bad. 
So, I decided to spend quality time checking and improving my site security. 
Sharing my findings here.

## Assessment... How bad I was

TIP: If you're just into the solution, skip this section ;)

For starters, I decided to check my personal site (sudobinbash.com) security.

My first impression was that my personal site security was... OK. I didn't have anything special. Just HTTPS + knowing what libraries I'm using + risk avoidance (just have a static site, without using fragmented architecture or schemes).

To serve my site, I use the free combo used and loved by any dev/slacker/blogger today: CloudFlare + Github Pages.

- CloudFlare is my CDN/DNS and I get a lot of security value from them due to their free HTTPS certs.
- Github is my code repo and also static host (Github Pages).

The pages are built using Hugo, a golang static site generator. If you're familiar with github pages native static site generator (Jekyll), just think about Jekyll on steroids with lases. Hugo provides great features like a custom template gallery and a super fast site generator (it takes 8-10ms to process any static site update... it's sick!).

```
I spent some time deciding in between Hugo and Gatsby. Gatsy is built on top of React and WebPack and is capable of pulling content from CMSless solutions using REST and GraphQL. I ended up with Hugo because I don't need a full CMS and I need to learn go. (note to myself: write something about this later).
```

To check my security. I used:
- securityheaders.com: To check my site headers
- Qualys: To check my site cryptography

And the results were quite disappointing. What I discovered:
1: I didn't have a single security header.
2: My DNSSEC was off.
3: I was supporting legacy crypto and not using the latest TLS implementations.

So I had to do something about it, but without spending a dime (because I'm cheap ;)

## Security fine tuning in CloudFlare

My first tactic was to fix everything in Cloudflare, using the awesome security tools they support.
With Cloudflare, I was able to:

- Configure DNSSEC
- Configure HTTPS only
- Use only TLS v1.2 and beyond

For security headers, I was thinking about using Cloudflare Workers – an awesome serverless implementation that run functions straight from your CDN, reducing latency – but Cloudflare started charging $5/month for it, so I had to go after something else.

After fine tuning CloudFlare, I solved the network security issues in my site pointed by Qualys and got to C- in security headers. That's awesome, but not enough...

## Netlify to the rescue

After working on CloudFlare, I had just the headers to figure out. For that, I decided to switch from github pages to Netlify.
Netlify is a static host built for developers. Their key differential is that they integrate directly into github, so you have your website automatically updated everytime you push something to your repo.

I used Netlify in the past for a project that didn't pan out, but I really got impressed with their capabilities. Their premium tier supports a lot of cool things, like generating staging websites for git branches, providing contact forms, etc.

To "migrate" from github pages to Netlify, I used this get started tutorial provided by Hugo.

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

1: I didn't have a single security header. (solved in Netlify)
2: My DNSSEC was off. (solved in CloudFlare)
3: I was supporting legacy crypto and not using the latest TLS implementations. (solved in CloudFlare)

The coolest part, is I could do everything for free and I learned a lot more about web security.
