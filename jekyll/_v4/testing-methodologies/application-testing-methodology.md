---
layout: classic-docs
title: Application Testing Methodology
description: Application Testing Methodology
categories: [testing-methodologies]
order: 1
hide: true
---

***


<h1>Background</h1>

Successful testing starts with succesful planning.  The intent of this document is to provide addiitonal guidance to best prepare you for testing and to meet your testing goals. This methodology assumes that some intitial performance testing is being done before automating testing in a CI/CD pipeline. Testing can be broken down into the following phases:

- TOC
{:toc}

This guide will speak from a high level on topics related to Load and Performance testing.


## Phase 1 - Planning, Test Configuration, and Validation

Phase 1 is further broken down into multiple parts.  Before you even run your first test, you should consider:

### Planning

- What are the most important parts of the system I am testing?
  - _Perhaps a handful of API endpoints are most critical to the proper function of your system. e.g. Login_
- What are my expected outcomes?
  - _Is there historical performance testing or events I can use to predict initial outcomes? e.g. During a new release we experienced heavy traffic of users trying our new features out, the response times of our login process was degraded_
- What other business rules should I consider?
  - _Has the business implemented any SLAs or other rules that relate to performance. e.g. certain response times, availability, etc._
- What environment am I testing?
  - _Production? A testing environment that is identical to production?  A testing environment that is scaled down?_
  - _A test environment that matches production is the most ideal, but not always available. What other considerations should I make to ensure that users aren't disrupted_


Once you have thought about these high level items, you should next move into creating and configuring your tests.

### Test Configuration

Here are some more things to think about before you even write/record your first test script.

- What are the most common actions my user take as they relate to the important components I want to test?
  - _Login generally tends to be one important part of an application.  What are others? If testing user journeys, what logical order do users of your application follow?_
  - _Don't get caught up on trying to test everything, focus on the most important parts.  So skip(for now) that feature that a handful of users use on a regular basis_
- How much load should I put on my target system?
  - _If testing individual components, such as API endpoints, think in terms of requests per Second_
  - _If testing user journeys, such as users visiting page to page or taking multiple actions, think in terms of concurrent users._
- What tests patterns will I execute?
  - _Different tests will tell you different things.  Refer to this article for an explanation of the different patterns:_
  - _Generally, this order will work for most when establishing a testing process:  Baseline test, stress test, load test, spike test (if applicable)_
- What data do I need?
  - _Using the login example, testing the same user logging in will usually only test how well your system can cache responses.  What data do you need to be parameterized to support your test executions?_

Once you have those things in mind, you can start by either writing your scripts by hand or using the built in HAR file converter offered by k6 to put together your test scripts. **It's best to start small and then expand your test coverage.  Treat testing as you would code you are writing for system by breaking it down into smaller, more manageable pieces.** A lot of value can be found in simple tests against the most important components.

**Best Practice Alert:** k6 supports modularization.  The script you write to perform a login function with a random user, can later become it's own module in your load testing library. This can be shared with your team through version control or other means.

### Validation

As you are writing your scripts and before you run them at a larger scale it's important to run validations (smoke tests) to ensure that the test works as intended.  These tests are typically run with a very low number of virtual users for a very short amount of time.  For speed, it's recommended you run these locally with output to `stdout`.  In other words, don't run a validation using cloud infrastructure or streamed into the cloud for analysis.

## Phase 2 - Benchmark Testing, Scaling your tests, and Complex Cases

If you take the time to put in all your initial planning, the next steps become much easier to complete and require less specific instruction.

### Benchmark testing

It's not recommended to run your first test at full throttle and the maximum number of vitual users.  When testing it is critical to always be thinking comparitively.  Your first test should then be a **baseline test.** A baseline test is a test at an ideal number of concurrent users/requests per second and run for a long enough duration to produce a clear stable result. This baseline test will give you intitial performnace metrics for you to compare to future test runs.  The ability to compare will help highlight performance issues more quickly.

**For example:** Does your application generally receive 500 concurrent users at any given time? You should be running your baseline tests below that.

### Scaling your Tests

After your benchmark tests are complete, it's time to start to run larger tests to detect performance problems. You should utilize the Stress Test pattern to scale / step your tests up through different levels of concurrency to detect degradations.  As you find these degradations and make adjustments you will iterate on the test multiple times until a satisfactory level of performance is met.

Regardless of the complexity of your test cases, the testing you do in this step will provide the most information to take action on to improve performance. Once you have finished iterating through tests at this stage, verified by acceptable test runs that finish, you should move to a load test pattern to verify the system under test can handle an extended duration at the goal level of load.

### Complex Cases

If, like most users, you started by writing small simple test cases, now is the time to expand them.  You may want to repeat this second phase to look for deeper performance problems, ones that could only show up when multiple components are under load.

**Best practice Alert:** If you have been modularizing your test scripts up to this point, this step may be as easy as including them into a "master" test case.

## Phase 3 - Ongoing performance Testing

The final step in our methodology is one that is long term.  Up until now you have spent a good deal of time and effort creating your test cases, executing your tests, fixing various issues, and improving performance. You can continue to receive returns on the time investment made by including performance testing into your Continuous integration pipelines or just by running tests on a regular basis manually. 
