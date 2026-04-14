Dompdf Accessibility Fork
=========================

[![Build Status](https://github.com/mhadar/dompdf/actions/workflows/test.yml/badge.svg)](https://github.com/mhadar/dompdf/actions/workflows/test.yml)
[![PHP Versions Supported](https://poser.pugx.org/mhadar/dompdf/require/php)](https://packagist.org/packages/mhadar/dompdf)
[![Latest Release](https://poser.pugx.org/mhadar/dompdf/v)](https://packagist.org/packages/mhadar/dompdf)
[![Total Downloads](https://poser.pugx.org/mhadar/dompdf/downloads)](https://packagist.org/packages/mhadar/dompdf)
[![License](https://poser.pugx.org/mhadar/dompdf/license)](https://packagist.org/packages/mhadar/dompdf)

**`mhadar/dompdf` is a Dompdf fork focused on tagged, accessibility-oriented PDF output.**

This repository starts from the upstream [dompdf/dompdf](https://github.com/dompdf/dompdf)
HTML-to-PDF renderer and adds support for generating richer PDF metadata and
structure needed by accessibility tooling such as PAC. The PHP namespace remains
`Dompdf\...`, so existing application code can stay largely unchanged while you
switch Composer/package sources to this fork.

This fork is intended for projects that need Dompdf-compatible rendering plus
additional building blocks for accessible PDF output, including structure tags,
document metadata, embedded fonts, and logical reading order data.

## Fork-specific additions

- Tagged PDF support using the `_tag` HTML attribute to map source nodes to PDF structure roles.
- Structure tree generation, including `StructTreeRoot`, `ParentTree`, and `IDTree` output.
- Marked-content sequences (`MCID`) for tagged content.
- Support for `alt` text and `_actual-text` values where the structure output supports them.
- Metadata propagation from HTML, including `<title>`, `<meta name="author">`, `<meta name="keywords">`, `<meta name="description">`, and `<html lang>`.
- PDF/A helper mode through `isPdfAEnabled`, including metadata/output-intent related output already implemented by the fork.
- Font embedding support compatible with accessible/PAC-oriented workflows when the document uses embeddable fonts.

## Notes about accessibility

This fork gives you the PDF-side hooks required for accessibility-oriented
output, but valid PAC/PDF accessibility results still depend on the input HTML,
correct tagging, meaningful alternate text, document language, and using fonts
that can be embedded. In other words, this fork helps generate the required PDF
objects; it does not automatically turn arbitrary HTML into a fully compliant
accessible PDF.

## Core Dompdf features

- Handles most CSS 2.1 and a few CSS3 properties, including `@import`, `@media`, and `@page` rules.
- Supports most presentational HTML 4.0 attributes.
- Supports external stylesheets, either local or through HTTP/FTP via PHP stream wrappers.
- Supports complex tables, including row and column spans, separate and collapsed border models, and individual cell styling.
- Image support for GIF, PNG, BMP, JPEG, and basic SVG use cases.
- No dependency on external PDF libraries when using the CPDF backend.
- Inline PHP support.

## Requirements

- PHP 7.1 or higher
- DOM extension
- MBString extension
- `dompdf/php-font-lib`
- `dompdf/php-svg-lib`

Some required dependencies have their own downstream dependencies.

### Recommendations

- GD for image processing
- Imagick or Gmagick to improve image-processing performance for some image types
- OPcache for better runtime performance

## Fonts, metadata, and language

Accessible PDFs usually require embedded fonts, document metadata, and language
information. This fork already reads and forwards several HTML-level signals:

- `<title>` becomes the PDF title.
- `<meta name="author">`, `<meta name="keywords">`, and `<meta name="description">` are mapped into PDF info/XMP fields.
- `<html lang="...">` is forwarded as the document language.

For non-Windows-ANSI text you still need to use a font with the required glyphs.
Dompdf can embed referenced fonts as long as they are available to the renderer
and declared with CSS `@font-face` rules or otherwise loaded by the font system.

## Installation

### Install with Composer

```bash
composer require mhadar/dompdf
```

Make sure your Composer autoloader is loaded early in your application:

```php
require 'vendor/autoload.php';
```

### Download a packaged release

Download a packaged archive from this fork's releases page:

- https://github.com/mhadar/dompdf/releases

Packaged releases include the fork plus its dependencies and autoloader.

```php
require_once 'dompdf/autoload.inc.php';
```

### Install from git

```sh
git clone https://github.com/mhadar/dompdf.git
cd dompdf
composer install
```

## Quick start

```php
use Dompdf\Dompdf;
use Dompdf\Options;

$options = new Options();
$options->setIsPdfAEnabled(true);

$dompdf = new Dompdf($options);

$html = <<<'HTML'
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Accessible Example</title>
  <meta name="author" content="Example App">
  <meta name="description" content="Tagged PDF example">
  <meta name="keywords" content="pdf, accessibility, tagged pdf">
</head>
<body _tag="Document">
  <h1 _tag="H1">Account overview</h1>
  <p _tag="P">This paragraph is part of the logical structure tree.</p>
  <img _tag="Figure" src="logo.png" alt="Company logo">
  <span _tag="Span" _actual-text="IBAN">AT12 3456 7890 1234 5678</span>
</body>
</html>
HTML;

$dompdf->loadHtml($html);
$dompdf->setPaper('A4');
$dompdf->render();
$dompdf->stream('accessible.pdf');
```

## Accessibility-oriented HTML attributes

This fork recognizes additional attributes that are useful for tagged PDF output:

- `_tag`: sets the structure type written to the PDF for the element.
- `_actual-text`: replacement text for supported tagged content such as `Span`, `TD`, and `Lbl`.
- `alt`: alternate description used for figures where applicable.
- `_placement`: optional placement hint forwarded into the structure data when present.

Use these attributes deliberately and keep the structure tree semantically
correct. For example, prefer `H1`-`H6`, `P`, `L`, `LI`, `Lbl`, `LBody`, `Table`,
`TR`, `TH`, `TD`, `Span`, `Figure`, and `Artifact` where they match the content.

## Resource reference requirements

To protect potentially sensitive information, Dompdf restricts local and remote
resource loading.

Files accessed through web-based protocols have the following requirements:

- The `isRemoteEnabled` option must be set to `true`.
- PHP must either have the `curl` extension enabled or `allow_url_fopen` set to `true`.

Files accessed through the local file system have the following requirement:

- The file must fall within the path(s) specified for the Dompdf `chroot` option.

## Limitations and caveats

- Table cells are not pageable, so a table row must fit on a single page.
- Elements are rendered on the active page when they are parsed.
- Raw inline SVG support remains limited.
- CSS flexbox is not supported.
- CSS Grid is not supported.
- A single Dompdf instance should not be used to render more than one HTML document because persisted parsing/rendering state can affect later renders.
- `isPdfAEnabled` helps with metadata/output-intent related output, but font embedding and semantic tagging decisions are still up to the caller.

## Upstream credit

This fork is based on the upstream Dompdf project by the Dompdf community and
continues to use the same LGPL-2.1 license. See [AUTHORS.md](AUTHORS.md) for
upstream contributors.
