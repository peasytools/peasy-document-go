# peasy-document-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-document-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-document-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-document-go)](https://goreportcard.com/report/github.com/peasytools/peasy-document-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Go client for the [PeasyFormats](https://peasyformats.com) API -- convert between Markdown, JSON, YAML, CSV, and other document formats with tools for format identification, MIME type lookup, and structured data transformation. Zero dependencies beyond the Go standard library.

Built from [PeasyFormats](https://peasyformats.com), a comprehensive document conversion toolkit offering free online tools for transforming between structured data formats. The glossary covers document formats from lightweight Markdown to structured JSON and YAML, while guides explain format conversion strategies and encoding best practices.

> **Try the interactive tools at [peasyformats.com](https://peasyformats.com)** -- [Markdown to HTML](https://peasyformats.com/doc/markdown-to-html/), [YAML/JSON Converter](https://peasyformats.com/doc/yaml-json-converter/), [Format Identifier](https://peasyformats.com/doc/format-identifier/), and more.

<p align="center">
  <img src="demo.gif" alt="peasy-document-go demo -- Markdown, JSON, YAML, and CSV conversion tools in Go terminal" width="800">
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [Document Conversion Tools](#document-conversion-tools)
  - [Browse Document Format Reference](#browse-document-format-reference)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About Document Formats](#learn-more-about-document-formats)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

## Install

```bash
go get github.com/peasytools/peasy-document-go
```

Requires Go 1.21+.

## Quick Start

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasy "github.com/peasytools/peasy-document-go"
)

func main() {
	client := peasy.New()
	ctx := context.Background()

	// List available document tools
	tools, err := client.ListTools(ctx, nil)
	if err != nil {
		log.Fatal(err)
	}
	for _, t := range tools.Results {
		fmt.Printf("%s: %s\n", t.Name, t.Description)
	}
}
```

## What You Can Do

### Document Conversion Tools

Document format conversion is a core workflow for developers, data engineers, and content authors. Converting Markdown to HTML powers static site generators and documentation systems. Transforming JSON to YAML (and vice versa) is essential for configuration management across Kubernetes, Docker Compose, and CI/CD pipelines. CSV-to-JSON conversion bridges the gap between spreadsheet data and web APIs.

| Tool | Description | Use Case |
|------|-------------|----------|
| Markdown to HTML | Convert Markdown syntax to semantic HTML | Static sites, documentation pipelines |
| YAML/JSON Converter | Bidirectional YAML and JSON transformation | Kubernetes configs, API payloads |
| Format Identifier | Detect file format from content or extension | File upload validation, data pipelines |

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasy "github.com/peasytools/peasy-document-go"
)

func main() {
	client := peasy.New()
	ctx := context.Background()

	// Fetch the Markdown to HTML conversion tool
	tool, err := client.GetTool(ctx, "markdown-to-html")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("Tool: %s\n", tool.Name)           // Markdown to HTML converter
	fmt.Printf("Category: %s\n", tool.Category)   // Document conversion category

	// List supported document formats and their MIME types
	formats, err := client.ListFormats(ctx)
	if err != nil {
		log.Fatal(err)
	}
	for _, f := range formats.Results {
		fmt.Printf("%s (%s): %s\n", f.Name, f.Extension, f.MimeType)
	}

	// List available format conversions from Markdown
	conversions, err := client.ListConversions(ctx, &peasy.ListConversionsOptions{Source: str("markdown")})
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("Found %d conversion paths from Markdown\n", len(conversions.Results))
}

func str(s string) *string { return &s }
```

Learn more: [Markdown to HTML Tool](https://peasyformats.com/doc/markdown-to-html/) · [YAML/JSON Converter](https://peasyformats.com/doc/yaml-json-converter/) · [File Format Conversion Guide](https://peasyformats.com/guides/file-format-conversion-guide/)

### Browse Document Format Reference

The document format glossary provides clear definitions for structured data formats, markup languages, and serialization standards. Understanding the differences between JSON and YAML syntax, when CSV is preferable to JSON for tabular data, and how Markdown flavors (CommonMark, GFM) differ helps developers choose the right format for each use case.

| Glossary Term | Description |
|---------------|-------------|
| Markdown | Lightweight markup language for creating formatted text with plain-text syntax |
| CSV | Comma-separated values format for tabular data interchange |
| JSON | JavaScript Object Notation for structured data serialization |
| YAML | Human-readable data serialization format used in configuration files |

```go
// Browse document format glossary terms
glossary, err := client.ListGlossary(ctx, &peasy.ListOptions{Search: str("encoding")})
if err != nil {
	log.Fatal(err)
}
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition) // Document format definition
}

// Read in-depth guides on format conversion strategies
guides, err := client.ListGuides(ctx, &peasy.ListGuidesOptions{Category: str("conversion")})
if err != nil {
	log.Fatal(err)
}
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel) // Guide title and difficulty level
}
```

Learn more: [Markdown Glossary](https://peasyformats.com/glossary/markdown/) · [JSON Glossary](https://peasyformats.com/glossary/json/) · [How to Convert Markdown to Other Formats](https://peasyformats.com/guides/how-to-convert-markdown-to-other-formats/)

### Search and Discovery

The unified search endpoint queries across all document tools, glossary terms, guides, and supported file formats simultaneously. This is useful for building editor plugins, documentation search, or data pipeline tools that need to discover conversion capabilities.

```go
// Search across all document tools, glossary, and guides
results, err := client.Search(ctx, "csv to json", nil)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Found %d tools, %d glossary terms\n",
	len(results.Results.Tools),
	len(results.Results.Glossary)) // Cross-content document format search results
```

Learn more: [CSV Glossary](https://peasyformats.com/glossary/csv/) · [YAML Glossary](https://peasyformats.com/glossary/yaml/) · [All Document Guides](https://peasyformats.com/guides/)

## API Client

The client wraps the [PeasyFormats REST API](https://peasyformats.com/developers/) with typed Go structs and zero external dependencies.

```go
client := peasy.New()
// Or with a custom base URL:
// client := peasy.New(peasy.WithBaseURL("https://custom.example.com"))
ctx := context.Background()

// List tools with pagination
tools, _ := client.ListTools(ctx, &peasy.ListOptions{Page: 1, Limit: 10})

// Get a specific tool by slug
tool, _ := client.GetTool(ctx, "markdown-to-html")
fmt.Println(tool.Name, tool.Description)

// Search across all content
results, _ := client.Search(ctx, "csv to json", nil)
fmt.Printf("Found %d tools\n", len(results.Results.Tools))

// Browse the glossary
glossary, _ := client.ListGlossary(ctx, &peasy.ListOptions{Search: str("encoding")})
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Discover guides
guides, _ := client.ListGuides(ctx, &peasy.ListGuidesOptions{Category: str("conversion")})
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel)
}

// List file format conversions
conversions, _ := client.ListConversions(ctx, &peasy.ListConversionsOptions{Source: str("markdown")})

// Get format details
format, _ := client.GetFormat(ctx, "csv")
fmt.Printf("%s (%s): %s\n", format.Name, format.Extension, format.MimeType)
```

Helper for optional string parameters:

```go
func str(s string) *string { return &s }
```

### Available Methods

| Method | Description |
|--------|-------------|
| `ListTools(ctx, opts)` | List tools (paginated, filterable) |
| `GetTool(ctx, slug)` | Get tool by slug |
| `ListCategories(ctx, opts)` | List tool categories |
| `ListFormats(ctx, opts)` | List file formats |
| `GetFormat(ctx, slug)` | Get format by slug |
| `ListConversions(ctx, opts)` | List format conversions |
| `ListGlossary(ctx, opts)` | List glossary terms |
| `GetGlossaryTerm(ctx, slug)` | Get glossary term |
| `ListGuides(ctx, opts)` | List guides |
| `GetGuide(ctx, slug)` | Get guide by slug |
| `ListUseCases(ctx, opts)` | List use cases |
| `Search(ctx, query, limit)` | Search across all content |
| `ListSites(ctx)` | List Peasy sites |
| `OpenAPISpec(ctx)` | Get OpenAPI specification |

Full API documentation at [peasyformats.com/developers/](https://peasyformats.com/developers/).
OpenAPI 3.1.0 spec: [peasyformats.com/api/openapi.json](https://peasyformats.com/api/openapi.json).

## Learn More About Document Formats

- **Tools**: [Markdown to HTML](https://peasyformats.com/doc/markdown-to-html/) · [YAML/JSON Converter](https://peasyformats.com/doc/yaml-json-converter/) · [Format Identifier](https://peasyformats.com/doc/format-identifier/) · [All Tools](https://peasyformats.com/)
- **Guides**: [File Format Conversion Guide](https://peasyformats.com/guides/file-format-conversion-guide/) · [How to Convert Markdown to Other Formats](https://peasyformats.com/guides/how-to-convert-markdown-to-other-formats/) · [All Guides](https://peasyformats.com/guides/)
- **Glossary**: [Markdown](https://peasyformats.com/glossary/markdown/) · [CSV](https://peasyformats.com/glossary/csv/) · [JSON](https://peasyformats.com/glossary/json/) · [YAML](https://peasyformats.com/glossary/yaml/) · [All Terms](https://peasyformats.com/glossary/)
- **Formats**: [All Formats](https://peasyformats.com/formats/)
- **API**: [REST API Docs](https://peasyformats.com/developers/) · [OpenAPI Spec](https://peasyformats.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-document](https://pypi.org/project/peasy-document/) | `pip install "peasy-document[all]"` |
| **TypeScript** | [peasy-document](https://www.npmjs.com/package/peasy-document) | `npm install peasy-document` |
| **Rust** | [peasy-document](https://crates.io/crates/peasy-document) | `cargo add peasy-document` |
| **Ruby** | [peasy-document](https://rubygems.org/gems/peasy-document) | `gem install peasy-document` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | Go | Description |
|---------|------|-----|----|-------------|
| peasy-pdf | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-pdf-go) | PDF merge, split, rotate, compress -- [peasypdf.com](https://peasypdf.com) |
| peasy-image | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-image-go) | Image resize, crop, convert, compress -- [peasyimage.com](https://peasyimage.com) |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-audio-go) | Audio trim, merge, convert, normalize -- [peasyaudio.com](https://peasyaudio.com) |
| peasy-video | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-video-go) | Video trim, resize, thumbnails, GIF -- [peasyvideo.com](https://peasyvideo.com) |
| peasy-css | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-css-go) | CSS minify, format, analyze -- [peasycss.com](https://peasycss.com) |
| peasy-compress | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-compress-go) | ZIP, TAR, gzip compression -- [peasytools.com](https://peasytools.com) |
| **peasy-document** | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-document-go) | **Markdown, HTML, CSV, JSON conversion -- [peasyformats.com](https://peasyformats.com)** |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [Go](https://pkg.go.dev/github.com/peasytools/peasytext-go) | Text case conversion, slugify, word count -- [peasytext.com](https://peasytext.com) |

## License

MIT
