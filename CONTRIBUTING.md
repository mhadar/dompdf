# How to contribute

- [Getting help](#getting-help)
- [Submitting bug reports](#submitting-bug-reports)
- [Contributing code](#contributing-code)
- [Releasing](#releasing)

## Getting help

Questions, implementation discussions, and informal bug reports for this fork
should go through the fork repository:

- Discussions: https://github.com/mhadar/dompdf/discussions
- Issues: https://github.com/mhadar/dompdf/issues

When asking for help, mention that you are using the `mhadar/dompdf` fork and,
if relevant, whether your question is about tagged PDF output, PAC validation,
metadata, font embedding, or upstream Dompdf behavior.

## Submitting bug reports

Please search the issue tracker first to avoid duplicates.

When reporting a bug, include:

- The exact fork version, branch, or commit hash.
- Your PHP version and operating system.
- The rendering backend you use, if relevant.
- A minimal HTML/CSS example that reproduces the problem.
- If the issue is accessibility-related, include the expected structure/tagging result and any PAC or validator output that demonstrates the failure.

For accessibility/PAC-related reports, it is especially helpful to include:

- The HTML snippet with `_tag`, `_actual-text`, `alt`, and related attributes.
- Whether `isPdfAEnabled` is enabled.
- Which fonts are referenced and whether they are embedded.
- The exact validator/tool version you used.

## Contributing code

- Make sure you have a GitHub account.
- Fork https://github.com/mhadar/dompdf
- Make your changes on the `master` branch or on a focused feature branch.
- Add or update tests when your change affects behavior.
- Submit a pull request against this fork.

Please keep fork-specific changes clearly documented, especially when they
change PDF structure output or metadata behavior compared with upstream Dompdf.

## Releasing

To create a packaged release for this fork:

1. Tag the version and push it.
2. Create the packaged release locally:

```sh
php bin/create-release.php
```

3. Upload the generated zip file from `build/` to the GitHub release page for this fork.
