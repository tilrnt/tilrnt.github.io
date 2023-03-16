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


It's a common to use Cloudfront as a content delivery network to cache assets such as javascript and images. Another possible use case is to route traffic to an Application Load Balancer.

The issue is the origin will receive the IP address of the cloudfront host and not the real IP of the client.

One option is to create a Cloudwatch Function that writes a request header of `true-client-ip`:
{% highlight javascript %}
function handler(event) {
    var request = event.request;
    var clientIP = event.viewer.ip;

    //Add the true-client-ip header to the incoming request
    request.headers['true-client-ip'] = {value: clientIP};

    return request;
}
{% endhighlight %}

We can attach the function to the cloudfront cache policy.

The issue with the above is the need to maintain an additional dependency.

A more manageable solution would be to add custom headers to a cache policy and attach it to the relevant behaviour of the distribution. 

Based on [AWS Cloudfront forward headers] and [Adding Cloudfront request headers] documentation, we managed to implement it in terraform:

{% highlight terraform %}
resource "aws_cloudfront_cache_policy" "example" {
  name        = "example-policy"
  comment     = "test comment"
  default_ttl = 50
  max_ttl     = 100
  min_ttl     = 1

  parameters_in_cache_key_and_forwarded_to_origin {
    cookies_config {
      cookie_behavior = "none"
    }

    # Declare the list of custom headers to forward to the origin
    headers_config {
      header_behavior = "whitelist"
      headers {
        items = ["Host", "CloudFront-Viewer-Address"]
      }
    }

    query_strings_config {
      query_string_behavior = "none"
    }
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

    cache_policy_id  = aws_cloudfront_cache_policy.example.id
    path_pattern     = "/*"
  }
  ...

}
{% endhighlight %}

We added the `Host` and `Cloudfront-Viewer-Address` headers to the cache policy. The `Cloudfront-Viewer-Address` header contains the IP address of the client and port.

In my use case it appends the client IP in the format of `x_forwarded_for: <my ip>` in the origin's logs.
