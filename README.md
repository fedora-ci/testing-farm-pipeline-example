# Testing Farm pipeline example

This is a simple example of a pipeline that is integrated with the [Testing Farm](https://testing-farm.gitlab.io/api/).


## Requirements

Make sure that the [Fedora CI pipeline library](https://github.com/fedora-ci/jenkins-pipeline-library) is [configured in your Jenkins](https://www.jenkins.io/doc/book/pipeline/shared-libraries/#global-shared-libraries).
You can also cache the library in Jenkins -- see the `Cache fetched versions on controller for quick retrieval` option in the global configuration.

You will also need 2 plugins installed:
* [HTTP Request Plugin](https://www.jenkins.io/doc/pipeline/steps/http_request/)
* [Webhook Step](https://plugins.jenkins.io/webhook-step/)

