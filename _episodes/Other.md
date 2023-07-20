---
title: "Other"
teaching: 15
exercises: 15
questions:
- What else can I do to improve my software?
objectives:
- Learn optional ways to improve your repository
keypoints:
- There are always more ways to improve your repository
---
# Other

The following sections include information that isn't essential but can often be very helpful.


## Including badges in your README.md

Badges are small images that provide information about your project.
They are often used to ~~show off~~ show that a project is well maintained and tested.
GitHub has a [badging guide](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge), basically to add a badge to your `README.md`, use the following format:

```
https://github.com/<OWNER>/<REPOSITORY>/actions/workflows/<WORKFLOW_FILE>/badge.svg
```

So for the `python_project_template` we used:

```
![tests](https://github.com/adacs-australia/python_project_template/actions/workflows/pytest.yml/badge.svg)
![docs](https://github.com/adacs-australia/python_project_template/actions/workflows/sphinx_docs.yml/badge.svg)
```

## Zenodo

As software becomes more important to our data intensive field, it also become important to receive credit for your work developing software.
If you have time you can write a software paper and submit it to software dedicated journals such as [The Journal of Open Source Software](https://joss.theoj.org/) or to an astronomy journal that accepts software papers such as [Publications of the Astronomical Society of Australia](https://www.cambridge.org/core/journals/publications-of-the-astronomical-society-of-australia) or [Monthly Notices of the Royal Astronomical Society](https://academic.oup.com/mnras).

However, if you don't have time to write a paper, you can still get a DOI for your software using [Zenodo](https://zenodo.org/).
Once you create a Zenodo account you can link it to your GitHub account and set it to monitor your selected repository.
Then every time you make a release on GitHub, Zenodo will automatically create a new version of your software and assign it a DOI.
There will be a DOI for your repository as a whole and for each release (useful for making it clear which version was used).

This makes it easier for your work to be referenced in publications.


## Versions

I personally think that software versioning is an under utilized tool in astronomy.
As our processing and methods get more complicated it is important that we compare like to like when it comes to version of software.
This is especially true when comparing different or updated data sets, you don't want to be unsure if the changes you are seeing is due to the software or the data.
For this reason it is important to version your software and make it clear what version of your software you are using in your publications so your results can easily be recreated.

There are many different ways to version your software, but the most common is [Semantic Versioning](https://semver.org/).
This is a system that uses three numbers to describe the version of your software.
Each number is a major, minor and patch version.
So if you start with version `1.0.0`, you would update to:
  - `2.0.0` if you made a major change. New major features or changed so much that you will not be able to run the software the same way.
  - `1.1.0` if you made a minor change. New minor features but it can still be run in a similar way.
  - `1.0.1` if you made a patch change. Oops you found a bug! You quickly fixed it and release a new version.

On the right side of your GitHub repository page you will see a Releases section where you can add a new one.
A good format for what you want to include in the release markdown is:

```
Brief summary of change

## New features
- Super easy to install now
- Other stuff

## Fixes
- Found a bug thanks to my testing automation
```
{: .language-markdown}

It doesn't matter if your release description isn't perfect, it is more make a release.


## Extra credit

The [template repo](https://github.com/ADACS-Australia/python_project_template) has two more implemented functions.
- `filter_by_name` which filters the DataFrame by source name
- `filter_by_declination` which filters the DataFrame by a minimum and maximum declination

Improve your version of the repository so that the `filter_and_plot` has arguments for using these new functions you have written.
Create tests and documentation for these new functions then compare your implementation and the template repo.
Which implementation do you think is best?
Which is easier to use?
Which software is easier to understand?
Which tests are more robust?
