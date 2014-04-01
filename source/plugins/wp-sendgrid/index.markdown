---
layout: page
title: "WP SendGrid"
date: 2014-03-31 23:55
comments: true
sharing: true
footer: true
---

WordPress relies on email for various notifications, such as new user messages and comments.
Many plugins also use email to notify blog owners about various events.
For anyone who develops in Windows, this is frustrating, because PHP can't send emails by default.

SendGrid is a service that lets you send emails through an API.
WP SendGrid is a plugin that extends WordPress' `wp_mail()` function so that it sends emails through SendGrid's API.
If you develop on Windows (or any other environment where you have trouble sending emails with WordPress), you're going to love WP SendGrid.

To install, enable WP SendGrid like you would any other WordPress plugin.
Enter your SendGrid credentials (you'll need a SendGrid account), and you should be ready to go.
If you wish, you can also choose between SendGrid's REST API and their SMTP servers, and whether to connect to SendGrid using a secure connection.

[download](http://wordpress.org/extend/plugins/wp-sendgrid/" "WordPress.org Plugin Page") | [source](https://github.com/codeawhile/wp-sendgrid "WP SenGrid on GitHub")