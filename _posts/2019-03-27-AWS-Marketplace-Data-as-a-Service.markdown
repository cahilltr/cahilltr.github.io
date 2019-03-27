---
layout: post
title:  "AWS Marketplace's First Data as a Service"
date:   2019-03-27 07:40:00 -0500
tags:
- AWS
- AWS-Marketplace
- Marketplace
- XMode
- DaaS
- Data-as-a-Service
- Microservices
- location
- GDPR
---

I currently work at [XMode Social](https://xmode.io/), where I help bring clean, GDPR compliant, location data to the world.  Recently, I was tasked with creating an AWS Marketplace offering that would allow subscribers to receive our data directly into the subscriber's S3 Bucket.  This writeup will describe how we created the offering and some of the beginner issues I ran into while creating our Data-as-a-Service (DaaS).  We currently offer the following location data available for subscription: USA, EU, Korea, Australia, Canada, Japan, Thailand, Brazil, and Mexico.

# How it was Created

I was lucky because 90% of the hard work had already been done.  XMode already has the processes in place to clean, process, regulate, and transfer our data.  To utilize these services, all I needed to do was create new daily exports that covered the locations being offered.  Once again, the team had done the hard work already by making the creation of the exports as generic as possible.  The only "hard work" that needed done was to create the ability for us to deliver data to any AWS customer/subscriber.  Once the hard work was done and a user has subscribed to our DaaS, our daily processes take over, delivering data to those who subscribed.

## Creating a Role

To enable the safest possible solution for us to deliver data to a subscriber's bucket, we need subscribers to create a role for XMode to assume.  To limit access, we asked that when creating the role, that the Principal be set to our xmode-data-transfer user.  We also added the external id to the role to prevent the Confused Deputy Problem.

### Issues with the Role's External Id

Because I didn't fully read documentation well before developing this part of the solution, when I first started testing, I was getting:

{% highlight bash %}
com.amazonaws.services.securitytoken.model.AWSSecurityTokenServiceException: 1 validation error detected: Value 'XMode Social' at 'externalId' failed to satisfy constraint: Member must satisfy regular expression pattern: [\w+=,.@:\/-]* (Service: AWSSecurityTokenService; Status Code: 400; Error Code: ValidationError; Request ID: uuid) 
{% endhighlight %}

The fix for this is easy enough, but we had to specifically search for the regex, [\w+=,.@:\/-]* to find the correct documentation.  Aside from a code fix on our end, we added the following note during our onboarding instructions, "...replace with input company name, without spaces or special characters except for the following: '=,.@:/-_ '".

## Creating a Policy for XMode

We wanted to create as restrictive a policy as possible. So we created a policy that allows us to only put, delete, and list objects and ACLs of those objects, but not allow any other operations.

Below is the json we ask our subscribers to create a policy for XMode to use.

{% highlight bash %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::bucket-name",
                "arn:aws:s3:::bucket-name/*"
            ],
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": [
                        "bucket-owner-full-control"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucket-name",
                "arn:aws:s3:::bucket-name/*"
            ]
        },
        {
            "Effect": "Deny",
            "NotAction": "s3:*",
            "NotResource": [
                "arn:aws:s3:::bucket-name",
                "arn:aws:s3:::bucket-name/*"
            ]
        }
    ]
}
{% endhighlight %}

This policy is then attached to the above role, which XMode assumes when we deliver our data.

## Resolving the Marketplace Token

When the subscriber decides to subscribe, we receive a token, that is then be resolved by AWS to a customer id and product code.  While testing, I found that the token received in the request has been URL encoded (not surprising, but I could not find anything in documentation).  After decoding the URL, we were able to use AWS's SDK to resolve the customer.

## Handling SNS Messages

AWS Marketplace sends 4 (really 5) SNS messages: subscribe-success, subscribe-fail, unsubscribe-pending, unsubscribe-success, and entitlement-updated.

### Subscribe Success
Just seconds after a new subscriber has joined, you can expected to receive an SNS "subscribe-success" message in the SNS  topic that has been assigned to the Marketplace product.  This message means that you can now start billing a subscriber for their use of the product.  When we receive this, we will enable exports if we have confirmed that we have the appropriate access to the subscriber's bucket.

### Subscribe Fail
"subscribe-fail" is an SNS message that we have not came across yet.  Due to that, we have a notification sent to the team to alert us to any such situation so that we may review our logs and contact AWS if needed.

### Unsubscribe Pending
When a customer unsubscribes, an "unsubscribe-pending" SNS message will be sent.  When that message has been sent, an hour remains to send any remaining charges.  At this point, we disable any process sending data to that subscriber at that point.

### Unsubscribe Success
After an "unsubscribe-pending" SNS message, a "unsubscribe-success" message will be sent, signaling the end of the ability to bill a former subscriber.  We receive this message and log it, but otherwise, no actions are taken for this message.

### Entitlement Updated

The "entitlement-updated" SNS message will only occur if you have an SaaS contracts product, but we log any instance that comes through so that we can flag a possible problem early.

## Metering

Metering is enabled by an "subscribe-success" message and disabled by an "unsubscribe-success" message.  We bill by "per 1000 daily users" sent.  Since we don't create an EC2 instance or image or actually offer a SaaS, we are not able to use the "MeterUsage" api, as it appears that only the subscriber can make that call (effectively, this lets the subscriber bill themselves via the SaaS application).  After a fair bit of struggle and some back and forth, it was determined that we needed to use the "BatchMeterUsage" to bill our subscribers from the Lambda, we use for our billing.  This is in contrast to what [Submitting Metering Records documentation](https://docs.aws.amazon.com/marketplacemetering/latest/APIReference/Welcome.html) has documented.  I have let the AWS team know about the discrepancy.

## Pricing Dimensions

According to [metering service documentation](https://docs.aws.amazon.com/marketplace/latest/userguide/metering-service.html), you are allowed to have 8 pricing dimensions.  After contacting AWS, the actual limit for SaaS based solutions subscriptions is 24 dimensions.

## Conclusion

Between AWS issues and hiccups getting our internal services to work together, alot of learning and work was done to get our Marketplace offering up off the ground.  A big thanks to my teammates for the work they did and the technical guidance they gave.
