# awscli image
Custom docker image with awscli installed

This image is based on the python:3.7.4-slim-buster image

Dockerfile adds the tools awscli and jq for processing `aws sts assume-role`

awscli is used for deployment of the website to an S3 bucket