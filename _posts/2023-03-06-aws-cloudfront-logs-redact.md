---
layout: post
title: Redact PII from Cloudfront logs
date: 2023-03-06 00:00:00
categories: aws terraform
---

[WAF Logging management]: https://docs.aws.amazon.com/waf/latest/developerguide/logging-management.html

In a recent project, I was asked to investigate how to redact or remove personal identiable information which are stored in cloudfront logs via `AWS WAF` for audit purposes.

Using a resource of `aws_wafv2_web_acl_logging_configuration` we are able to declare a `redacted_fields` block to identity which part of the request to remove. Within the block we can only declare an argument of `method`, `query_string`, `single_header` and `uri_path`.

Only the `single_header` argument takes a `name` attribute which is what I need in my use case.

By entering each of the header name in individual block, I was able to filter it out from the cloudfront logs:
{% highlight terraform %}
resource "aws_wafv2_web_acl_logging_configuration" "example" {
  log_destination_configs = [aws_kinesis_firehose_delivery_stream.example.arn]
  resource_arn            = aws_wafv2_web_acl.example.arn
  redacted_fields {
    single_header {
      name = "header-1"
    }

    single_header {
      name = "header-2"
    }
  }
}
{% endhighlight %}

To test that it works, I was able to trigger fake requests to be sent via Kinesis firehose which populated the logs. Then I accessed the logs via S3 and checked that the headers were marked with **REDACTED** if it has removed it.

More information can be found on the [WAF Logging management]{:target="_blank"}


