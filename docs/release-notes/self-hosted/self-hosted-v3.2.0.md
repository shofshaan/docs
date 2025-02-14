---
rss_title: Codacy release notes RSS feed
rss_href: /feed_rss_created.xml
description: Release notes for Codacy Self-hosted v3.2.0.
---

# Self-hosted v3.2.0

These release notes are for [Codacy Self-hosted v3.2.0](https://github.com/codacy/chart/releases/tag/3.2.0), released on December 17, 2020.

To upgrade Codacy, follow [these instructions](../../chart/maintenance/upgrade.md).

## Product enhancements

-   To overcome timeouts when loading the repositories list page on Codacy Self-hosted, it's now possible to configure how many repositories to load at a time. To do this, set a value lower than the default of 100 by configuring `codacy-spa.config.codacy.pagination.repositoriesLimit` on your `values-production.yml` configuration file. (CY-3352)
-   Now, Codacy has an improved user experience to add and manage organizations that supports displaying an indefinite number of organizations per Git provider. (CY-1938)

## Tool versions

This version of Codacy Self-hosted includes the tool versions below. The tools that were updated on this version are highlighted in bold:

-   Ameba 0.13.1
-   Bandit 1.6.2
-   Brakeman 4.3.1
-   bundler-audit 0.6.1
-   Checkstyle 8.37
-   Clang-Tidy 10.0.1
-   CodeNarc 1.6
-   CoffeeLint 2.1.0
-   Cppcheck 2.2
-   Credo 1.4.0
-   CSSLint 1.0.5
-   detekt 1.14.2
-   **ESLint 7.15.0 (updated from 7.14.0)**
-   Faux-Pas 1.7.2
-   Flawfinder 2.0.11
-   Gosec v2.3.0
-   Hadolint 1.18.2
-   Jackson Linter 2.10.2
-   JSHint 2.12.0
-   PHP_CodeSniffer 3.5.8
-   PHP Mess Detector 2.8.1
-   PMD 6.28.0
-   PMD (Legacy) 5.8.1
-   Prospector 1.2.0
-   PSScriptAnalyzer 1.18.3
-   Pylint 1.9.5
-   Pylint (Python 3) 2.6.0
-   remark-lint 7.0.1
-   Revive 1.0.2
-   RuboCop 0.82.0
-   Scalastyle 1.0.0
-   ShellCheck 0.7.1
-   Sonar C# 8.15
-   Sonar Visual Basic 8.15
-   SpotBugs 4.1.2
-   SQLint 0.1.9
-   Staticcheck 2020.1.6
-   Stylelint 13.8.0
-   SwiftLint 0.39.2
-   Tailor 0.12.0
-   TSLint 5.14.0
-   TSQLLint 1.11.1
