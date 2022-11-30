---
id: contributor-guidelines
title: How to Contribute
sidebar_label: Contributor guidelines
description: Prepare for your upcoming contribution
keywords:
  - docs
  - matic
  - polygon
  - contribute
  - contributor
  - contributing
image: https://wiki.polygon.technology/img/polygon-wiki.png
slug: orientation
---

:::tip
Feel free to [raise an issue on our Polygon Wiki repository](https://github.com/maticnetwork/matic-docs/issues)
:::

## Identify an area to Contribute to

There are several ways to identify an area where you can contribute to the Wiki:

- The easiest is to reach out to one of the [Wiki maintainers](/docs/contribute/community-maintainers) 
  saying “I want to help contribute to the Polygon Wiki”. They’ll work with you to find
  an area for you to contribute.
- If you have a specific contribution in mind but are unsure about it, confirm whether
  the contribution is appropriate by contacting one of the [Wiki maintainers](/docs/contribute/community-maintainers)directly.
- If you do not have a specific contribution in mind, you can also browse the issues
  labelled as `help wanted` on the [Polygon GitHub repos](https://github.com/maticnetwork).
- Issues that additionally have the `good first issue` label are considered ideal for
  first-timers.

## Add to the Polygon documentation

  - If you need to add or change anything in Polygon Wiki, please raise a PR
    against the `master` branch (kindly check the sample PR).
  - The documentation team will review the PR or reach out accordingly.
  - Repository: https://github.com/maticnetwork/matic-docs
  - Sample PR: https://github.com/maticnetwork/matic-docs/pull/360

:::tip
If you want to run our Wiki locally on your machine, check the section [running the Wiki locally](https://github.com/maticnetwork/matic-docs#run-the-wiki-locally). If you are adding a new document, it is recommended to just have a basic summary/introduction and a link to your Github or documentation for more details.
:::

## Git rules

We use `gitchangelog` for all of our repos for change logs. For that, we need to
comply with the following convention for commit message. There will be no merge if you are
not following this convention.

### Commit message convention

The following are suggestions to what might be useful to think about adding in your
commit messages. You might want to separate roughly your commits into big sections:

- by intent (for example: new, fix, change ...)
- by object (for example: doc, packaging, code ...)
- by audience (for example: dev, tester, users ...)

Additionally, you could want to tag some commits:

- As “minor” commits that shouldn’t get output to your changelog (cosmetic changes,
  small typo in comments...).
- As “refactor” if you don’t really have any significative feature changes. Thus this
  should not also be part of the changelog displayed to final user for instance, but
  might be of some interest if you have a developer changelog.
- You could tag also with “api” to mark API changes or if it's a new API or similar.

Try writing your commit message by targeting user functionality as often as you can.

:::note Example

This is a standard git log `--oneline` to show how these information could be stored:

```
* 5a39f73 fix: encoding issues with non-ascii chars.
* a60d77a new: pkg: added ``.travis.yml`` for automated tests.
* 57129ba new: much greater performance on big repository by issuing only one shell command for all the commits. (fixes #7)
* 6b4b267 chg: dev: refactored out the formatting characters from GIT.
* 197b069 new: dev: reverse ``natural`` order to get reverse chronological order by default. !refactor
* 6b891bc new: add utf-8 encoding declaration !minor
```

:::

For more info please refer to
[What Are Some Good Ways to Manage a Changelog Using Git?](https://stackoverflow.com/questions/3523534/good-ways-to-manage-a-changelog-using-git/23047890#23047890).

For more details, see [https://chris.beams.io/posts/git-commit/](https://chris.beams.io/posts/git-commit/).
