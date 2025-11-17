---
_edit_last: "2"
_last_editor_used_jetpack: block-editor
_thumbnail_id: "1732"
_wpas_done_all: "1"
author: yauheni.shybeka
topics:
  - development
cover:
  alt: continuous-integration-feature-image
  image: /wp-content/uploads/2020/03/continuous-integration-feature-image.png
date: "2020-03-15T13:15:00+00:00"
guid: https://itdranik.com/?p=924
language:
  - English
parent_post_id: null
post_id: "924"
post_translations:
  - pll_5e4809c57cc17
tags:
  - continuous-integration
  - github-actions
title: Continuous Integration is Simple. GitHub Actions
url: /en/continuous-integration-github-actions-en/

---
Hello dear reader!

All our lives have things that are embarrassing to remember about, not something to tell. Nevertheless, I have to say: until recently, [the coding-interview repository](https://github.com/itdranik/coding-interview/) hasn't used continuous integration capabilities. "What a disgrace!" - they will shout. "But who needs it at all?!" - others will object.

For my part, I propose to work out together why developers are increasingly using modern tools for continuous integration, instead of starting a fire rubbing two sticks together, and to add continuous integration into the coding-interview repository using [GitHub Actions](https://github.com/features/actions).

So what continuous integration is?

**Continuous integration (CI)** is an approach that involves making frequent changes to a codebase for identifying errors as quickly as possible, including during the integration process.

"Where are tests?" - an attentive reader will ask. Well, if the reader asks, why not answer? Tests are just one of the capabilities of detecting errors during frequent changes, making the process of continuous integration even more reliable. Therefore, de facto tests are just a part of the continuous integration process itself.

Today, there is a huge number of tools that simplify the process of continuous integration: Jenkins, Travis, Teamcity, GitLab CI/CD, GitHub Actions and others. Since our repository is located on GitHub, we will continue our acquaintance with GitHub tools, although the idea itself is the same everywhere:

1. Determine the **events** that will activate certain **jobs** when changes to the common code base are made;
1. For each **job**, determine the set of **actions** ( **steps**) that must be performed in the context of this job.

GitHub Actions are configured using [a configuration file](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions) in the YAML format, which should be located in the `.github/workflows/` directory. Here is an example configuration file:

In general, these configurations themselves are quite informative, but let's give some explanations:

- `on: [ push ]` describes the list of events that should activate this workflow. The conditions for events' activation triggers can be specified more strictly, for example, by the names of branches on which these events should or shouldn't be called;
- `jobs` describes the list of jobs that must be performed in the context of this workflow. In our case, we only created the job `test`;
- `runs-on: ubuntu-latest` determines the type of machine to run the job on;
- `steps` describes the list of actions (steps) to be performed:
  - `actions/checkout@v2` clones the source code to the execution server;
  - `actions/setup-dotnet@v1` sets up the environment `dotnet`;
  - `dotnet build cs-solvers --configuration Release` builds our project;
  - `dotnet test cs-solvers` runs tests.

To see the result of the workflow, select the `Actions` tab inside the repository:

{{< figure src="https://i2.wp.com/itdranik.com/wp-content/uploads/2020/03/continuous-integration-actions-tab.png?fit=680%2C97&ssl=1" alt="GitHub Actions tab" caption="GitHub Actions tab" >}}

And select the workflow of interest:

{{< figure src="https://i2.wp.com/itdranik.com/wp-content/uploads/2020/03/continuous-integration-workflow.png?fit=680%2C285&ssl=1" alt="An example of performing a GitHub workflow" caption="An example of performing a GitHub workflow" >}}

It remains to add to the root of `README.md ` file a link to the result of our workflow in the format:

That is, the following line:

As a result, we get this beautiful status icon below in the project description.

{{< figure src="/wp-content/uploads/2020/03/badge-1.png" alt="GitHub workflow status" caption="GitHub workflow status" >}}

It's just so grown-up serious! So, I think it's time to say goodbye. Good mood and stable builds!
