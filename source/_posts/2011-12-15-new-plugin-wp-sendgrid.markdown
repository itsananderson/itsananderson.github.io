---
layout: post
title: "New Plugin WP SendGrid"
date: 2011-12-15 09:49:43 -0700
comments: true
categories: WordPress SendGrid
---

A couple of months ago, I had the opportunity to attend an [API Hackday](http://www.apihackday.com/) event in Bloomington.
At the event, I created some cool WordPress tools using [Twilio](http://www.twilio.com/), but I ended up having some free time
(because Twilio's API is so easy to use!), so I wrote another plugin called [WP SendGrid](http://www.itsananderson.com/plugins/wp-sendgrid/) using the SendGrid API.

This plugin extends WordPress' `wp_mail()` function to use SendGrid's API for all emails.
This was useful to me because I've never been able to figure out how to get PHP to send emails in Windows.
In the past, when I've worked on projects that send emails, I always had to upload it to a Linux server to test the email functionality.
If anything didn't work, I had to edit locally, then upload again to test it.
After a few iterations, that gets really old.
With WP SendGrid, I can send emails from my local machine, so debugging issues is **much** easier.

Fast-forward a few months.
This week I decided to spend a little more time polishing the plugin.
I'd only spent about 45 minutes on it at the HackDay, which shows how simple it is to use SendGrid's API.
What I put together was enough to meet my needs, but I was using hardcoded values, and wasn't considering every edge-case.
After a few more hours of work, I had a complete plugin with a nice configuration UI.

If you develop for WordPress on Windows, or you have trouble sending email from PHP on another platform, you'll want to check this plugin out.
[Visit the plugin page](http://www.itsananderson.com/plugins/wp-sendgrid/).

If you run into any issues, let me know by submitting a ticket on the [GitHub](https://github.com/codeawhile/wp-sendgrid) project.