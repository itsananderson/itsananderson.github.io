---
layout: post
title: "Using jQuery and Gmail's Plus-Addressing to Fight Email Spam"
date: 2009-02-26 22:26:55 -0700
comments: true
categories: jQuery Gmail
---

Many people fight spam bots by obfuscating their emails (for example "joe [at] example [dot] com" or "joe@{REMOVETHIS}example.com"). This does an OK job, but it's also a royal pain for the visitor, who has to type in, or fix your email address if they want to send you an email. The ultimate spam fighting question arises: "How do you keep bots out while letting real users in". GMail's plus-addressing feature and jQuery might be able to bring us one step closer to an answer.

If you use Google's email services (GMail or Google Apps), you have a powerful email feature available. Plus-addressing means that bill&#64;gmail.com, bill+something&#64;gmail.com and bill+anything&#64;gmail.com all point to the same email address. When deciding what address to deliver the message to, Google drops everything after the plus sign. you can then actually use filters to move items to folders inside your mailbox (for example, you could use example+bank&#64;gmail.com for your banking). Doing this also makes it just a little harder (though not impossible) for bots to get your real email address.

By default, you probably don't want to display a "plus address" on a contact page, jQuery comes to our rescue here, allowing us to remove the "plus" part for users with JavaScript support. The following script looks for all email links and removes the "plus" portion from the href and the inner text.

```javascript
jQuery(document).ready(function(){
	jQuery('a[href^=mailto:]').each(removeSpamProtection);
});

function removeSpamProtection(index, element){
	var link = jQuery(element);
	link.attr('href', link.attr('href').replace(/\+[a-zA-Z]*/, ''))
		.text(link.text().replace(/\+[a-zA-Z]*/, ''));
}
```

Now all visitors with JavaScript support will see something like: 

```html
<a href="mailto:example@example.com">example@example.com</a>
```

Visitors without JavaScript support will see:

```html
<a href="mailto:example+spamblock@example.com">example+spamblock@example.com</a>
```

Normal visitors will see your real email address while bots and visitors without JavaScript will see something else. The bots will (hopefully) be confused by the plus sign. If not, they will be sending an email to an address that you can easily filter into a "maybe spam" folder.

So there you have it. A very short piece of JavaScript (thanks to the wonders of jQuery) that you can include in your WordPress theme or install as a separate plugin. 

What do you do to protect your email online? Do you have another method that works better? If so, please share it in the comments!