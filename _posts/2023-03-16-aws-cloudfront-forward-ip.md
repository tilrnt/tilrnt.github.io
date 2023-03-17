---
layout: post
title: Forward User IP from Cloudfront distribution
date: 2023-03-16 00:00:00
categories: aws cloudfront terraform
---

[AWS Cloudfront forward headers]: https://aws.amazon.com/premiumsupport/knowledge-center/configure-cloudfront-to-forward-headers/

[Adding Cloudfront request headers]: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/adding-cloudfront-headers.html#cloudfront-headers-viewer-location

[Controlling origin requests]: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/controlling-origin-requests.html#origin-request-create-origin-request-policy

[Add custom headers to origin requests]: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/add-origin-custom-headers.html

[HTTP request headers and CloudFront behavior]: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/RequestAndResponseBehaviorCustomOrigin.html#RequestCustomIPAddresses

[Cloudfront Policy blog post]: https://aws.amazon.com/blogs/networking-and-content-delivery/amazon-cloudfront-announces-cache-and-origin-request-policies/


In a recent project, I was troubleshooting an issue with a cloudfront distribution not passing the right request headers to the origin.

According to [HTTP request headers and CloudFront behavior]:

> CloudFront sets the value to the domain name of the origin that is associated with the requested object.
>

For `X-Forwarded-Proto`:
> CloudFront removes the header.
>

By default, Cloudfront will forward the IP of the distribution to the origin and not the real user's IP. In addition it will also remove the `X-Forwarded-Proto` header.

To resolve the issue we need to add those two headers to the distribution via a custom policy. 

But which policy group do we add it to? Cache policy ? Origin request policy?

To provide some context, recent changes to Cloudfront encourages the use of policies to edit the behaviour of the cache key, requests and response headers.

As per their [Cloudfront Policy blog post], Cache policies are generally used for caching assets. Origin request policies should be used instead to modify the request headers since it is invoked during a cache miss or revalidation. In my use case, I don't want the user's IP to be cached but instead forwarded it to the origin so a origin request policy is more aappropriate.

Since the cloudfront distribution was built using terraform, I was able to create a custom origin request policy and attach it to the distribution.


{% highlight terraform %}
resource "aws_cloudfront_origin_request_policy" "example" {
  name    = "example-policy"
  comment = "example comment"
  cookies_config {
    cookie_behavior = "none"
  }

  headers_config {
    header_behavior = "whitelist"
    headers {
      items = ["Host", "Cloudfront-Forwarded-Proto"]
    }
  }

  query_strings_config {
    query_string_behavior = "none"
  }
}

# Attach the above policy to the distribution
resource "aws_cloudfront_distribution" "s3_distribution" {
  ....


  enabled             = true
  is_ipv6_enabled     = true
  comment             = "Some comment"

  ...

  # Attach the above policy to the distribution
  default_cache_behavior {
    ...


    origin_request_policy_id  = aws_cloudfront_origin_request_policy.example.id
    path_pattern     = "/*"
  }
  ...

}
{% endhighlight %}

We added the `Host` and `Cloudfront-Forwarded-Proto` headers to the custom policy. 

In my use case it appends the client IP in the format of `x_forwarded_for: <my ip>` in the origin's logs.
