---
layout: classic-docs
title: How to use the Load Impact Version 4.0 Chrome extension to create test scripts
description: Instructions on how to use the Load Impact v4.0 Chrome extension to record real user behavior to aid in creation of  test scripts.
categories: [how-to-tutorials]
order: 1
---

***

<h1>Background</h1>


The Load Impact k6 Test Script Recorder Chrome extension allows you to generate the bulk of your test scripts simply by browsing like a user would on your site or web app.  The script created gives you a foundation which you can further edit, as required.

The Load Impact Chrome extension will capture everything – every single HTTP(s) request being loaded into the browser as you click – including ads, images, documents, etc., so you get a far more accurate read of what’s going on. Just press “record”, start browsing and when complete, the script will automatically upload to your Load Impact account.

_**Consider this:** The Chrome extension will not record other tabs or pop up windows. If you need to capture this information, you should check out [converting from a HAR file]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/)._

*Note:* Before you begin, please be sure to force refresh the Load Impact app to ensure you are on the most recent version of the Load Impact app.

_Here's how to start:_

  1. Download and install the [Load Impact k6 Test Script Recorder](https://chrome.google.com/webstore/detail/load-impact-k6-test-scrip/docmmckkhiefiadappjepjllcoemijpj)
  2. **Start a recording**
    Open the extension by clicking the Load Impact logo, and press "Start recording" to begin recording the current browser tab. Now browse like a user would or how you want our Virtual Users to execute. We suggest basing this on _real user behavior_ - don't try to visit every single page on your site or app. Focus on common journeys.
![Step 2]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/load-impact-version-4-chrome-extension/web-recorder-step-2.jpg)
  3. **Stop recording**
    When done, press "Stop recording", you'll be taken to the app to review the recorded test script
![Step 3]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/load-impact-version-4-chrome-extension/web-recorder-step-3.jpg)
  4. **Save your test scripts**
    Save the recorded script in any of your projects.
![Step 4]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/load-impact-version-4-chrome-extension/web-recorder-step-4.jpg)
  5. **You can now edit your script as necessary.**  Load Impact's in app IDE will update in real time to alert you of any syntax errors. You may need to edit your script to deal with CSRF tokens, adding advanced business logic, creating custom metrics, etc.
  6. **Once done, press run to start your test**


**Important things to note:**
- The default configuration will be a 12 minute test that ramps to 10 Virtual Users over 1 minute, stays at 10 for 10 minutes, then ramps back to 0 over 1 minute. You can change this in the stages section of the script. Refer to [this article]({{ site.baseurl }}4.0/test-scripting/load-test-ramping-configurations/) for more information on ramping configurations.
- No load zone is specified and it will run out of Ashburn by default. You can specify different load zones by adding a `ext.loadimpact.distribution` option. See [this article]({{ site.baseurl }}/4.0/test-running/cloud-execution/#test-configuration-options) for more information
- We have set `discardResponseBodies: true`.  This will discard all response bodies by default.
