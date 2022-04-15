---
layout: post
title: "Crasing into JMeter"
excerpt: "A post to test author overrides using a data file."
tags: [jmeter, automation, performance, acceptance, tests]
comments: true
---

JMeter is able to test many flavours of requests. Here the scope is just for HTTP requests, plus the tool itself and how it can manage to test multitenancy.

Apache [**JMeter**](http://jmeter.apache.org/) is an Apache project that can be used as a load testing tool for analyzing and measuring the performance of a variety of services, with a focus on web applications.[[1]]

JMeter can be used as a **unit test** tool for **HTTP**, TCP connections and OS Native processes.

The basics can be found in this [video](https://www.youtube.com/watch?v=8NLeq-QxkSw), it goes through the main concepts:

 1. [HTTP Request][2] which define the path and parameters of a VERB request. (REST verbs are GET, POST, PUT, DELETE)
 1. [HTTP Request Defaults][3] that enables all requests to share mainly web server name and port.
 1. [View Result Tree][4] to check the request history. Requests and responses.
* View Results Tree MUST NOT BE USED during load test as it consumes a lot of resources (memory and CPU).
 1. [Response Assertion][5] enable asserts based on pattern strings matching various fields of the response.
 * It uses [ORO Perl5 regular expressions][6].

> It's commom to see an `_` as part of the query string. This is part of the jQuery strategy to avoid caching. [StackOverflow](http://stackoverflow.com/questions/3687729/who-add-single-underscore-query-parameter)


## Jenkins Integration

Integration with continuous integration server is easier than expected. Making sure that JMeter is capable of running on the build server (java on the path), the required steps are the following:

### Generating the .jtl file
 1. Run JMeter on command line to generate the output
`C:\tools\jmeter\bin\jmeter -n -t .\%CODE_BRANCH%\Source\MultitenancyTests\MultitenancyTests.jmx -l .\%CODE_BRANCH%\Source\MultitenancyTests\MultitenancyTests.jtl`
 1. With the Performance Plugin, consume the output generated.

### The output need to be in xml format
Performance plugin doesn't expect jlt in csv format (Which is the default output of latest version of JMeter). It require a manual change in one configuration file.
Here is the [reference](http://stackoverflow.com/questions/19164629/jtl-file-is-not-getting-parsed-in-jenkin-for-jmeter), the main change is the output format, just remove the comment from the following line and change csv by xml:

    #jmeter.save.saveservice.output_format=csv

like this:

    jmeter.save.saveservice.output_format=xml

### Powershell vs batch

Jmeter need to run as batch, the powershelll is not working properly.
It's useful to changed as well the configuration to log the event when in CLI.

### Enable logs in order to make the steps more descriptive
```
#---------------------------------------------------------------------------
# Summariser - Generate Summary Results - configuration (mainly applies to non-GUI mode)
#---------------------------------------------------------------------------
#
# Define the following property to automatically start a summariser with that name
# (applies to non-GUI mode only)
summariser.name=summary
#
# interval between summaries (in seconds) default 30 seconds
#summariser.interval=30
#
# Write messages to log file
summariser.log=true
#
# Write messages to System.out
summariser.out=true
```

### Always clean the output file
The jtl file summary is used to decide if the build was a success or a failure.
If the jtl file stays on the build server, the average is always greater than 0% if once something went wrong. So it's important to flush the file.

```
echo "****************** ~ JMETER ~ ******************"
echo Command location: %cd%
echo Configuration file location: ".\%CODE_BRANCH%\Source\MultitenancyTests\MultitenancyTests.jmx"

echo Clear out old results...
del .\%CODE_BRANCH%\Source\MultitenancyTests\MultitenancyTests.jtl

echo Running...
C:\tools\jmeter\bin\jmeter -n -t .\%CODE_BRANCH%\Source\MultitenancyTests\MultitenancyTests.jmx -l .\%CODE_BRANCH%\Source\MultitenancyTests\MultitenancyTests.jtl

echo "****************** ~ JMETER ~ ******************"
```

[Reference...](http://lincolnloop.com/blog/load-testing-jmeter-part-2-headless-testing-and-je/)

### Reset Variable values.

Every time the thread is reused for another request, the local variables are kept and it can generate conflicts.
To avoid this is always good to clean up the vars after the steps are finished.

This can be done like this: http://jmeter.512774.n5.nabble.com/Removing-variables-td534792.html

Or this solution based on the documentation: **beanshell**

    vars.initialize();

### Passing variable

Sometime one would like to pass a variable in command line so that it could be used by jmeter test. In order to do this, in jmeter one should first define this variable as ${__P(TestCaseNumber)}. This in order to pass this variable in command line, one should do following

```
jmeter -n -t APITestScript.jmx -JTestCaseNumber=1234
```

### Miscellaneous data:
* [Learn JMeter in 60 minutes](https://www.youtube.com/watch?v=cv7KqxaLZd8
* [JMeter Assertions](http://blazemeter.com/blog/how-use-jmeter-assertions-3-easy-steps)
* [Performance testing JMeter](http://blogs.atlassian.com/2008/10/performance_testing_with_jmete/)
* [Performance tuning: Pulling a rabbit from a hat](http://craigsmith.id.au/2010/06/11/atlassian-summit-2010-day-1-wrapup/)
* [X-Path validator](http://www.freeformatter.com/xpath-tester.html)
* [Log Analysis](https://wiki.apache.org/jmeter/LogAnalysis)

  [1]: http://en.wikipedia.org/wiki/Apache_JMeter
  [2]: http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Request
  [3]: http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Request_Defaults
  [4]: http://jmeter.apache.org/usermanual/component_reference.html#View_Results_Tree
  [5]: http://jmeter.apache.org/usermanual/component_reference.html#Response_Assertion
  [6]: http://jakarta.apache.org/oro/api/org/apache/oro/text/regex/package-summary.html#package_description
