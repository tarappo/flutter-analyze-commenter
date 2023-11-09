
<img width="800" src="https://github.com/yorifuji/flutter-analyze-commenter/assets/583917/588272c1-f2d3-4b8d-be3e-20812caa42f4">

# Flutter Analyze Commenter

Comment Flutter Analyze results in pull requests.

<img width="579" alt="image" src="https://github.com/yorifuji/flutter-analyze-commenter/assets/583917/8bcaf4da-82e3-4e79-8f4a-6baa4d35192a">

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

```yaml
jobs:
  flutter-analyze:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write # required to add comment on PR
    steps:
      # checkout your repository and install flutter

      # run flutter analyze with --write option
      - name: Run flutter analyze
        run: flutter analyze --write=flutter_analyze.log

      # use flutter-analyze-commenter
      - name: Comment PR by flutter-analyze-commenter
        uses: yorifuji/flutter-analyze-commenter@v1
        if: ${{ !cancelled() }}            # run this step even if flutter analyze fails
        with:
          analyze_log: flutter_analyze.log # file path of flutter analyze log
          verbose: true                    # optional: default is false
```

## How it works

When developing with Flutter, suppose you have the following code snippet:

```dart
void main() {
  final String y1 = 1;
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});
...
```

This may lead to issues being flagged in the VSCode Problems pane, similar to the following:

![image](https://github.com/yorifuji/flutter-analyze-commenter/assets/583917/8cdb1f14-5e55-4182-86e9-a3d906499de8)

These would typically be identified by the `flutter analyze` command in the following manner:

```shell
% flutter analyze
Analyzing flutter_application_9...                                      

   info • Use 'const' for final variables initialized to a constant value • lib/main.dart:4:3 • prefer_const_declarations
warning • The value of the local variable 'y1' isn't used • lib/main.dart:4:16 • unused_local_variable
  error • A value of type 'int' can't be assigned to a variable of type 'String' • lib/main.dart:4:21 • invalid_assignment

3 issues found. (ran in 0.9s)
```

The `flutter-analyze-commenter` GitHub Action takes the output from `flutter analyze` and posts it as comments directly on the pull request. This ensures that all the issues that would appear in your local Problems pane are now visible to reviewers directly in the PR, thus streamlining the review process and making it easier to address issues before merging code changes.

1. Reads the log file generated by `flutter analyze`.
2. Parses the results using a custom JavaScript script within the `actions/github-script` action.
3. Posts comments on the pull request with any issues found, directly through the GitHub API.
4. Ensures that duplicate comments are not posted for unchanged issues and that outdated comments are removed.

By utilizing `actions/github-script`, this action remains within the GitHub Actions environment, making it secure and efficient without the need for additional permissions or setup.

## Motivation

Running flutter analyze within GitHub Actions is a commendable CI practice that significantly improves code quality. However, trawling through Action execution logs to find flutter analyze results can be quite inconvenient. While utilizing a combination of [Danger](https://github.com/marketplace/actions/danger-action) and [danger-flutter_lint](https://github.com/mateuszszklarek/danger-flutter_lint) can facilitate displaying these results as PR comments — and these tools are indeed powerful — they also introduce a considerable overhead due to their multifunctionality and numerous dependencies. There was a clear need for a more streamlined tool, one that's solely focused on presenting flutter analyze results within pull requests, without the extra baggage.

This is where flutter-analyze-commenter comes in: a minimalistic, easy-to-use solution that seamlessly integrates with your CI/CD workflow.

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
