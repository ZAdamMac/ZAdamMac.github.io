---
layout: post
title: "Polaris II: Electric Boogaloo"
date: 2020-07-06 00:30 -300
categories: Web
---

As I alluded to in yesterday's [Lab Notes update](https://zadammac.github.io/labnotes/2020/07/05/lab-notes-01.html), I was revisiting my web development and service stack. Previously, I had a two-machine approach to doing development and hosting for [Kensho Security Labs](https://www.kenshosec.com) and [Sanity Line](https://www.sanityline.net), where I was doing most of the development in a [highly simplified local apache arrangement](https://www.kenshosec.com/Guides/devapache.html), and hosting the resulting static sites [using my improvised server](https://zadammac.github.io/projects/2017/09/24/Improvising-A-Server.html).

As you can imagine, this was a clumsy but workable arrangement, and like a TV presenter chef not showing his prep work, I'm deliberately omitting a whole mess of batch scripts - on both machines - that made everything flow as smoothly as possible - clumsy though it remained. Around the end of last year I determined that this was enough hassle to need streamlining. I had also noted that my outdated and stale Apache installation was starting to score lower and lower on various tests. It was my hope that redesigning the stack in [docker](https://docker.org) would give me an easy way to do what I was actually trying to do:
- Develop in a sandboxed test environment that I could break without damaging anything.
- Make the process of promoting both configuration changes and site content into production as seamless as possible
- Provide me an ability to use VCS with a remote repo to do rudimentary change management and offsite backup of as much of the server as possible.

Docker actually made this absurdly easy, and I wound up making use of `docker-compose` to make this as simple as possible by:
- setting up an `nginx-proxy` service to handle reverse proxying for virtual hosts and streamline the process of securing CA-backed certs.
- setting up individual services that sit behind the nginx-proxy service taking advantage of Virtual Host functionality to keep the different sites and environments live.
- Amending my mess of bash scripts to properly streamline everything.

Like any good individual developer, I'm declaring victory slightly early, so I also want to look at the *lessons learned* in each step and the *work to be done*, since there's always something else you can add by way of refinements.

## Setting up the nginx-proxy service
I'm not going to lie - I am not nearly clever enough (or, more accurately, motivated enough) to invent the reverse-proxy-and-automatic-cert-replacement service whole-cloth. I followed [this guide] more or less to the letter, with thanks to friend [@comrade_eevee][https://twitter.com/ComradeEevee] for pointing me in that direction.

This guide uses Nginx reverse proxying, some automation, and child containers to do the following few things:
- listen on a docker network on the host machine for new services coming up.
- Uses environment variables declared in those containers' configuration to add those machines to its own virtual hosts list, and
- Uses a container in its service to automatically manage requesting and renewing certificates for TLS for any virtual host it's addressing, backed by the Let's Encrypt Certificate Authority.

## Setting up the Kenshosec and Sanity Line Services
In theory, I could have put any kind of services behind the proxy to host my websites, including Apache, which I was already using. However, after plenty of past experimentation over the winter of trying to get apache2 playing nicely in a docker container with as much of my original setup as possible, I decided that if I was starting over from scratch, I would start over this week with Nginx. I was a little apprehensive - both sites rely on Server Side Includes to handle their header and footer bars, which are repeated across all pages in the site.

Turns out, turning on SSI for this particular use case was as simple as adding the stanza `SSI on;` to the main config file.

I've included the `docker-compose.yml` for one of the two services I created below:

```yaml
version: '3'

services:
  kenshosec-prod:
    image: nginx
    expose:
      - 80
      - 443
    environment:
      VIRTUAL_HOST: www.kenshosec.com
      LETSENCRYPT_HOST: www.kenshosec.com
      LETSENCRYPT_EMAIL: certs@kenshosec.com
    volumes:
      - ./ksec-prod:/usr/share/nginx/html
      - ./prod-config.conf:/etc/nginx/nginx.conf
  kenshosec-dev:
    image: nginx
    expose:
      - 80
      - 443
    volumes:
      - ./ksec-test:/usr/share/nginx/html
      - ./test-config.conf:/etc/nginx/nginx.conf
    environment:
      VIRTUAL_HOST: test.kenshosec.com
      LETSENCRYPT_HOST: test.kenshosec.com
      LETSENCRYPT_EMAIL: certs@kenshosec.com

networks:
    default:
        external:
            name: nginx-proxy

```

For a simple static-content service, that's really all you need. You can really put any kind of web service you want behind the reverse proxy, and I somewhat intend to do this at some point in the future as I experiment with different site generation methods and CMS platforms, to try and get away a bit from hacking away in gedit and the bad habits that breeds.

Note that in my case one other method was also needed - I had to add the `test` subdomain for each of my domains so that they also point properly at the VPS.

## Bash and VCS Changes
A few years of using my previous, unweildy stack, left me with a jumbled mess of poorly workflows for dealing with configuration changes or content updates, none of which were especially well tracked or controlled.

As a quick hack to celebrate tonight's progress, I changed my main utility, `sitepush`, so that it now copies the site content files as well as the appropriate configuration and `docker-compose.yaml` files over to the server with rsync. It executes extremely quickly as a result, and because of the way I'm set up locally I'm only writing into the configuration and content for test (except for the compose files themselves, I suppose.)

I bolted lazy version control onto the side, for now, by creating a private github repo. I can track (signed) commits for both configuration and content changes. The long-term goal is to eventually eliminate sitepush altogether - it could be replaced with a frequently-running cron job to pull the same files down from Github, copying them over only if they're validly signed, which would ideally let me check the results within a minute or two of a push.

## Future Work

As much as this move resolved some potential issues (and improved my security posture in terms of TLS configuration), it also created a new problem - my local dev environment is actually quite different now than even the "test" environment in Docker. I'm not running nginx locally to the machine I use to edit these websites, and I'm leary to install yet another passive service on this machine for me to forget to update or shut down. Creating a method to seamlessly work on content that's rapidly refreshed into the test environment is going to wind up taking priority sooner rather than later, as is hardening those two services.

On the other hand, I've also set myself up for future success in terms of adding and testing new services on that machine. If I want to start to set up a private cloud or develop Tapestry's companion service, Loom, I can now.

If for some reason you enjoy Tapestry, PETI, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially,  your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac). If you're feeling the need, [I also enjoy coffee](https://ko-fi.com/KenshoSec).

