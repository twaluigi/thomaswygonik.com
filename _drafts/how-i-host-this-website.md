---
layout:     post
title:      How I Host this Website

---

One of the simplest ways of hosting a website is to spin up a Linux server, install Apache or Nginx, and add the code. Another even simpler way is to use something like [GitHub Pages](https://pages.github.com/) to create a clean blog relatively easily. GitHub Pages allows the use of [Jekyll](https://jekyllrb.com/) to generate the static site.

Another static site hosting solution is AWS S3. S3 makes it easy to upload our site's code and host it. One drawback is lack of https support directly with S3. However, AWS provides the service CloudFront which _does_ have HTTPS support, with the added advantage of distributing our website files around the world.

