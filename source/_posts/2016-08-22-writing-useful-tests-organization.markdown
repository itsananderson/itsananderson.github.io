---
layout: post
title: "Writing Useful Tests: Organization"
date: 2016-08-22 20:31:32 -0700
comments: true
categories: Testing
---

Most people agree that writing tests is an important part of software development.
However, not all tests are created equal.

Working on a number of projects over the years, I've run into a few pitfalls and gotchas that I want to cover in the next few blog posts.
This won't be a comprehensive guide to testing.
Others have already done a better job of that than I ever could.
I just want to write down the pain points I've run into, and the techniques that have worked for me.

Organizing Your Tests
---

Figuring out how to organize your tests can be a daunting task, especially when you're starting a new project.
Do you keep your tests beside your feature code, or do you have two distinct "src" and "test" directories?
Do you break up your test files by feature? By class? By unit?

I don't think there's one right answer to these questions, but I know what has and hasn't worked for me on my projects.

### Test Location

I'm a fan of keeping your tests close to your source code.
That doesn't necessarily mean every `foo.js` has a `foo.test.js` beside it, but I do prefer having source code and test code in the same project, rather than keeping them in distinct source folders.

This collocation of tests and source code encourages the mindset of writing tests and source code being one and the same.
I've worked on projects where the tests and source were in very different parts of the source repository, and the typical workflow was to write your code, and then figure out how to test it.
Keeping your tests and source code in the same project doesn't necessarily solve this problem completely, but at least it helps keep your tests readily available as you're writing your feature or bug fix.

### Breaking Up Your Tests

Breaking up your tests into distinct files (and folders if your project is large enough) is a good way to keep them manageable.
I've worked on projects that had thousands of lines of code in the same test file, and it was a nightmare to find and update tests within those enormous files.
By contrast, projects that have been broken into smaller focused test files made it much easier to find where a test should be added or updated.

How you break up your tests will partly depend on your programming language and the type of project.
I've found that a good place to look for test grouping is the setup code for your tests.
If you have the same few lines of setup code in several of your tests, consider putting them in the same test file with a shared `beforeEach` block.
What this looks like will depend on your language and test framework.

By contrast, if you find that your `beforeEach` block is doing a lot of setup that's not used by most of the tests, consider breaking the file apart into the pieces that use the different components of the setup block.

When you open a test file, it should be clear from the file name what feature(s) are being tested. This will help you navigate your test codebase when it's time to update or add tests.

### Arrange, Act, Assert

When talking about test structure, it's common to discuss the three parts of a test: Arrange, Act, and Assert.

* Arrange - Set up the variables, objects, and mocks you'll use in your test
* Act - Perform the action that you're attempting to test
* Assert - Verify that the action had the expected result

This organization structure for tests is probably a cliché, but it's a useful way to think about how you organize your individual tests.
Writing tests that have clear distinctions between these three sections will make it easier for other maintainers to understand what you're trying to test.

I've sometimes had tests where these distinctions were hard to make, or where I felt like I needed to intermingle assertions in the other parts of the tests.
This usually meant that I was testing too much, and needed to break into multiple tests.

Conclusion
---

Organizing your tests makes them easier to understand, navigate, and update, which makes it much easier and more enjoyable for you and your team to maintain them.
Putting effort and thought into your test structure now will pay dividends in the future.

Next time I'll talk about naming your tests and writing useful test assertion messages.
