---
layout: classic-docs
title: Test configuration options
description: The available test configuration options
categories: [test-scripting]
order: 3
---

***

<h1>Background</h1>

Options allow you to configure how k6 will behave during test execution.  You can specify options in four different ways:

- As part of your script code, so it is version controlled
- As a command line flag
- As a enviroment variable
- As part of a config file.

There is an order of precedence when evaluating options, which is as follows: defaults < config file < exported script options < environment variables < command-line flags. Options from a greater level can be used to overwrite those from a lower level. CLI flags have the greatest priority.

For example, duration can be specified in 4 different ways:

- use the command-line flag `-d 10s`
- define `K6_DURATION` as an environment variable
- set the `duration: "10s"` option in the script
- set the `duration: "10s"` option in the config file

Here is an example of a test with configurations set within the script:

{% highlight js linenos %}
import http from "k6/http";

export let options = {
  hosts: {
    "test.loadimpact.com": "1.2.3.4"
  },
  stages: [
    { duration: "1m", target: 10 },
    { duration: "1m", target: 20 },
    { duration: "1m", target: 0 }
  ],
  thresholds: {
    http_req_duration: ["avg<100", "p(95)<200"]
  },
  noConnectionReuse: true,
  userAgent: "MyK6UserAgentString/1.0"
};

export default function() {
  http.get("http://test.loadimpact.com/");
}

{% endhighlight %}

Here are the same options set via a `config.json` file

{% highlight json linenos %}

{
  "hosts": {
    "test.loadimpact.com": "1.2.3.4"
  },
  "stages": [
    { "duration": "1m", "target": 10 },
    { "duration": "1m", "target": 30 },
    { "duration": "1m", "target": 0 }
  ],
  "thresholds": {
    "http_req_duration": ["avg<100", "p(95)<200"]
  },
  "noConnectionReuse": true,
  "userAgent": "MyK6UserAgentString/1.0"
}

{% endhighlight %}

Or, set the options via environment variables or command-line flags:

```shell

$ K6_NO_CONNECTION_REUSE=true K6_USER_AGENT="MyK6UserAgentString/1.0" k6 run ~/script.js

$ k6 run ---no-connection-reuse --user-agent "MyK6UserAgentString/1.0" ~/script.js

```

**Select the options you wish to learn more about from the following table:**
- TOC
{:toc}



## duration - Specify test length

`duration` or `--duration value` or `-d value` or `K6_DURATION`

A string specifying the total duration a test run should be run for. During this time each VU will execute the script in a loop.

Default: null

Examples

export let options = {
    duration: "3m"
};


## ext - Load Impact Cloud specific options

### distribution

When running a cloud execution test you can configure from which load zones the traffic should be generated. See the [cloud execution]({{ site.baseurl }}{% link _v4/test-running/cloud-execution.md %}) article for more information.

### name

By default the name of your test will be based on the filename of the main JS file you specify when executing k6 on the command line.

```shell
k6 run -o cloud script.js
```

In the above example, the name of the test would be "script.js". To override the default and specify a name you set the Load Impact extension option `name`, like this:

{% highlight js linenos %}
export let options = {
    ext: {
        loadimpact: {
            name: "My test name"
        }
    }
};
{% endhighlight %}

### project

<div class="callout callout-warning" role="alert">
    <b>Note that your API token is scoped to a specific organization</b><br>
    If you are part of more than one Load Impact organization you need to generate an API token for each organization as the API token is currently per-organization. You can do this by first switching to the specific organization using the organization/project switcher in the left sidebar and then clicking on "Integrations" in the left sidebar menu (ending up on <a href="https://app.loadimpact.com/account/token" class="alert-link">this page</a>).
</div>

By default tests and test runs will be created and run under your default project, in your default organization. To create and run tests under a different project, whether under your default organization or one you've been invited to, you have two options:

1. You can specify the project ID in the script options:
{% highlight js linenos %}
export let options = {
    ext: {
        loadimpact: {
            projectID: 123456
        }
    }
}
{% endhighlight %}
<ol start="2"><li>You can set the <code>K6_CLOUD_PROJECT_ID</code> environment variable when running the test.</li></ol>

You find the ID of a Load Impact project by selecting a k6 project in the UI and looking in the URL bar of your browser, the `12345` in `https://app.loadimpact.com/projects/12345/dashboard` is the project ID.


## hosts - Overiding DNS Resolution

## insecureSkipTLSVerify - ignore TLS certificate verification

## maxRedirects - Specify the max number of redirects

## batch - Set the max number of parallel connections

## batchPerHost Set the max number of connctions per host

## noConnectionReuse - Specify if k6 should disable keep-alive connections

## noVUConnectionsReuse - Specify if k6 should reuse TCP connections between iterations of a VU

## paused - Specify if a test should start in a paused state

## rps - Maximum number of rps to make per second across all VUs

## stages - Specify the ramping profile of VUs

## tags - Specify tags that should be set test wide across all metrics

## throw - Specify whether to throw errors on failed HTTP requests

## blacklistIPs - Specify IP ranges from being called

## summaryTrendStats - Define stats for trend metrics

## tlsAuth - List of TLS client certificate configuration options

## tlsCipherSuites - List of cipher suites allowed to be used

## tlsVersion - String or object specifying what is allowed to be used with a server

## userAgent - String specifying the user-agent

## httpDebug - Option to log all requests and responses

## vus - Number specifying the number of VUs to run concurrently

## vusMax - Number specifying the max number of Virtual Users

## systemTags - Specify which System Tags will be collected

## setupTimeout - Specify how long `setup()` can run before termination

## teardownTimeout - Specifying how long `teardown()` can run before termination







## Naming your test



## Specifying how many virtual users to run

There are two ways that you can specify how many Virtual Users (VUs) you want to run.

- Specify a constant number
- Specify an arbitrary number of ramp-up, constant and ramp-down stages

### Constant VU load

To specify a simple constant number of VUs you set the `vus` option:

{% highlight js linenos %}
export let options = {
    vus: 100,
    duration: "60s"
};
{% endhighlight %}

*Note: that you must also specify `duration` to set the test length. Each VU will run the default (aka main) function over and over (like a while-true loop) for the length of the test.*

**NOTE:** If `VUs` are specified as written above and dynamic stages are also used as described in the following section. Tests will **START** at the constant VU level specified.

### Dynamic VU load

To specify an arbitrary number of ramp-up, constant and ramp-down stages you set the `stages` option:

{% highlight js linenos %}
export let options = {
    stages: [
        { target: 100, duration: "60s" },
        { target: 100, duration: "60s" },
        { target: 0, duration: "60s" },
    ]
};
{% endhighlight %}

The above `stages` configuration would result in a VU load as follows:
- Start of at 0 users
- For the first 60s, VUs ramp up from 0 to 100
- For the next 60s, VUs stay constant at 100
- For the last 60s, VUs ramp down to 0

See [load test ramping configurations]({{ site.baseurl }}{% link _v4/test-scripting/load-test-ramping-configurations.md %}) for more examples.

## Specifying from which load zones to generate traffic


## Sending results to a specific project


## Overriding DNS

Sometimes you have a system that is responding to requests with a certain `HOST` header but the system is not hosted under the same IP(s) listed in DNS. For those situations you can instruct k6 to resolve certain hostnames to specific IP addresses when making requests in the test:

{% highlight js linenos %}
export let options = {
    hosts: {
        "test.loadimpact.com": "1.2.3.4",
        "api.loadimpact.com": "5.6.7.8"
    }
}
{% endhighlight %}

## Setting thresholds for pass/fail results

See the dedicated article about [thresholds]({{ site.baseurl }}{% link _v4/test-scripting/thresholds.md %}) for more information.

## Turning off TLS certificate checking

{% highlight js linenos %}
export let options = {
    insecureSkipTLSVerify: true
}
{% endhighlight %}

## Only allow specific TLS versions

Specifying a specific version to allow:

{% highlight js linenos %}
export let options = {
    tlsVersion:  "tls1.2"
};
{% endhighlight %}

Specifying a min and max version to allow:

{% highlight js linenos %}
export let options = {
    tlsVersion: {
        min: "ssl3.0",
        max: "tls1.2"
    }
};
{% endhighlight %}

## Only allow specific TLS ciphers

For a full listing of available ciphers go [here](https://golang.org/pkg/crypto/tls/#pkg-constants).

{% highlight js linenos %}
export let options = {
    tlsCipherSuites: [
        "TLS_RSA_WITH_RC4_128_SHA",
        "TLS_RSA_WITH_AES_128_GCM_SHA256",
    ]
};
{% endhighlight %}

See the k6 docs on [options](https://docs.k6.io/docs/options) for more information.

**Next**: [Main function]({{ site.baseurl }}{% link _v4/test-scripting/main-function.md %})
