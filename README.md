
<img width="800" src="https://github.com/yorifuji/flutter-analyze-commenter/assets/583917/588272c1-f2d3-4b8d-be3e-20812caa42f4">

# Flutter Analyze Commenter

Flutter Analyze Commenter is a GitHub Action that posts `flutter analyze` results as comments on pull requests.

<img width="707" src="https://github.com/yorifuji/flutter-analyze-commenter/assets/583917/420c2c61-6f69-41b5-8b83-aa6e154317e2">

## Features

- **Automated PR Reviews**
  - Automatically scans the Flutter analyze log file and adds line-specific comments to pull requests, simulating a manual code review process.
- **Easy to Use**
  - Just set the path to your Flutter analyze log file, and the action handles the rest.
- **Direct Integration with GitHub Actions**
  - Designed as a native GitHub Action, it directly utilizes the GitHub ecosystem, providing a seamless integration with your CI/CD pipeline without the need for third-party tools.
- **Small Footprint**
  - Designed to be minimalistic, only doing what is necessary to comment on the PR based on Flutter analyze results.

## Usage

Example workflow

```yaml
name: fluttera-analyze-commenter example 

on: pull_request  # fluttera-analyze-commenter only supports run by pull request

jobs:
  flutter-analyze:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write # required to add comment on PR
    steps:
      # checkout repository and setup flutter environment
      ...

      # run flutter analyze with --write option
      - run: flutter analyze --write=flutter_analyze.log

      # use flutter-analyze-commenter
      - uses: yorifuji/flutter-analyze-commenter@v1
        if: ${{ !cancelled() }}                        # required to run this step even if flutter analyze fails
        with:
          analyze_log: flutter_analyze.log             # file path for analyze log
          verbose: false                               # optional
```

## How it works

If you wrote code like the following in VSCode,

```diff
diff --git a/lib/main.dart b/lib/main.dart
index dda5554..0456e5b 100644
--- a/lib/main.dart
+++ b/lib/main.dart
@@ -1,6 +1,8 @@
 import 'package:flutter/material.dart';
 
 void main() {
+  final String y1 = 1;
+
   runApp(const MyApp());
 }
```

problems will be shown.

![image](https://github.com/yorifuji/flutter-analyze-commenter/assets/583917/8cdb1f14-5e55-4182-86e9-a3d906499de8)

These are based on the output from the `flutter analyze` command.

```shell
% flutter analyze
Analyzing flutter_application_9...                                      

   info • Use 'const' for final variables initialized to a constant value • lib/main.dart:4:3 • prefer_const_declarations
warning • The value of the local variable 'y1' isn't used • lib/main.dart:4:16 • unused_local_variable
  error • A value of type 'int' can't be assigned to a variable of type 'String' • lib/main.dart:4:21 • invalid_assignment

3 issues found. (ran in 0.9s)
```

This GitHub Action parses the log and posts it as comments directly on the pull request. This ensures that all the issues that would appear in your local Problems pane are now visible to reviewers directly in the PR, thus streamlining the review process and making it easier to address issues before merging code changes.

1. Reads the log file generated by `flutter analyze`.
2. Parses the results using a custom JavaScript script within the [actions/github-script](https://github.com/marketplace/actions/github-script) action.
3. Posts comments on the pull request with any issues found, directly through the GitHub API.
4. Ensures that duplicate comments are not posted for unchanged issues and that outdated comments are removed.

## Motivation

Running flutter analyze within GitHub Actions is a commendable CI practice that significantly improves code quality.

However, trawling through Action execution logs to find flutter analyze results can be quite inconvenient. While utilizing a combination of [Danger](https://github.com/marketplace/actions/danger-action) and [danger-flutter_lint](https://github.com/mateuszszklarek/danger-flutter_lint) can facilitate displaying these results as PR comments — and these tools are indeed powerful — they also introduce a considerable overhead due to their multifunctionality and numerous dependencies.

There was a clear need for a more streamlined tool, one that's solely focused on presenting flutter analyze results within pull requests, without the extra baggage.

This is where Flutter Analyze Commenter comes in: a minimalistic, easy-to-use solution that seamlessly integrates with your CI/CD workflow.

## Known issues

Currently, there are no reported issues. If you encounter any problems, please open an issue in the repository.

## Development

TBD

### Setup

TBD

### Debug

TBD

## License

MIT
