---
title: "Website Infrastructure"
date: 2020-03-04T09:30:52-08:00
draft: true
author: Tom
---

I wanted a simple way of hosting this website, but also wanted the challenge of automating as much as I could. I decided to host this website on AWS using an S3 static site. There's more infrastructure than just the S3 bucket, and deployment is all automated.

## Topology
![Website Topology](/images/website_topology.png)

## Terraform
I use a multi-account infrastructure to keep development separate, but this isn't necessary.

## Certificates
CloudFront can use certificates provisioned through AWS Certificate Manager (ACM). The setup is a little tricky using Terraform.

## Redirects
What if you want a subdomain to redirect back to the real domain? For instance, <https://www.tomwygonik.com> -> <https://tomwygonik.com>? I set this up with an additional cloudfront distribution (not required) and S3 bucket. The S3 bucket defintion for redirect goes like this:

```HCL
resource "aws_s3_bucket" "redirect_www_bucket" {
  provider = "aws.site_account"
  bucket   = "www.${var.site_fqdn}"
  acl      = "private"
  website {
    index_document = "index.html"
    error_document = "error.html"
    routing_rules = <<EOF
    [{
    "Redirect": {
		"HostName" : "${var.site_fqdn}",
		"HttpRedirectCode" : "301",
		"Protocol" : "https"}
		}]
		EOF
  }
}
```

## Conclusion
It's important to note that this setup does *not* populate the site with content, it only provisions the infrastructure. You'll still need to upload files to the S3 bucket (this can also be automated with CI).

If you want to check out my full setup, the code and Gitlab CI Pipeline can be found at <https://gitlab.com/twaluigi/terraform-tomwygonik-website>

## Future work
In the future, this could be templated out as one module for the whole static site. Then, you could provide just the relevant variables and spin up an entirely new infrastructure
