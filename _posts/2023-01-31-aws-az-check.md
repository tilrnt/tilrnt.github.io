---
layout: post
title: Get list of Availability Zones in a given region
date: 2023-01-31 00:00:00
categories: aws aws-cli
---

A recent terraform deployment for vpsc it failed with an error of `Resource not available in given availability zone`.

I used the `aws-cli` to get the list of azs from the given region in question:
{% highlight shell %}
aws ec2 describe-availability-zones --region eu-west-1
{% endhighlight %}

It returns a list as follows:
{% highlight shell %}
{
    "AvailabilityZones": [
        {
            "State": "available",
            "OptInStatus": "opt-in-not-required",
            "Messages": [],
            "RegionName": "eu-west-1",
            "ZoneName": "eu-west-1a",
            "ZoneId": "euw1-az2",
            "GroupName": "eu-west-1",
            "NetworkBorderGroup": "eu-west-1",
            "ZoneType": "availability-zone"
        },
        {
            "State": "available",
            "OptInStatus": "opt-in-not-required",
            "Messages": [],
            "RegionName": "eu-west-1",
            "ZoneName": "eu-west-1b",
            "ZoneId": "euw1-az3",
            "GroupName": "eu-west-1",
            "NetworkBorderGroup": "eu-west-1",
            "ZoneType": "availability-zone"
        },
        {
            "State": "available",
            "OptInStatus": "opt-in-not-required",
            "Messages": [],
            "RegionName": "eu-west-1",
            "ZoneName": "eu-west-1c",
            "ZoneId": "euw1-az1",
            "GroupName": "eu-west-1",
            "NetworkBorderGroup": "eu-west-1",
            "ZoneType": "availability-zone"
        }
    ]
}
{% endhighlight %}

That allows me to troubleshoot the missing az and switch to another region with the right number of azs.