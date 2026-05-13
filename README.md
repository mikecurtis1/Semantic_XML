# Semantic XML

## Introduction

This project explores XML as a semantic document format rather than simply a container for metadata. Unlike XML systems that primarily describe external information about content—titles, identifiers, categories, timestamps—this project models structural and semantic relationships embedded directly within the text itself.

The example document is Shakespeare’s *Sonnet XXIX*, encoded as structured XML and visually rendered directly in the browser using CSS. No transformation pipeline is required. The XML document itself remains the primary artifact, while CSS controls presentation independently.

The project therefore demonstrates several aspects of the original XML philosophy:

* semantic structural markup
* schema-based validation
* separation of structure and presentation
* browser rendering through CSS
* declarative document modeling

Rather than transforming XML into HTML, the browser acts only as a renderer. The XML document preserves the literary structure while CSS provides visual interpretation.

---

## Why a Sonnet?

A sonnet is an unusually effective example for schema design because its structure is tightly coupled to meaning.

In many XML domains, schemas primarily describe metadata surrounding content. A music catalog, for example, may describe titles, composers, dates, instrumentation, or movement organization, but the schema itself does not define the semantic identity of the musical work.

A sonnet differs because the structure is part of the meaning. The number of lines, grouping into quatrains and couplets, rhyme relationships, and cadence patterns are not incidental properties; they define the literary form itself.

This allows the schema to move beyond simple metadata description toward semantic structural enforcement.

The sonnet model therefore becomes a compact demonstration of several broader data modeling concerns:

* ordered hierarchy
* bounded structure
* constrained repetition
* explicit relationships
* formal validation
* semantic grouping

In this sense, the project is less about poetry itself than about how formal structure can encode meaning.

---

## Structural Modeling

The XML schema models the sonnet as a constrained literary form:

* exactly three quatrains
* exactly one couplet
* fixed line counts
* ordered structure throughout
* required rhyme metadata
* semantic distinction between quatrain and couplet

Because the structure closely tracks meaningful literary units, schema validation becomes semantically significant:

* missing quatrain → incomplete form
* incorrect grouping → structural inconsistency
* invalid cadence ordering → formal violation

The project intentionally stops short of enforcing a rigid Shakespearean rhyme sequence such as:

`ABAB CDCD EFEF GG`

because doing so would overconstrain the model and fail to reflect the more fluid internal rhyme relationships present in *Sonnet XXIX* itself.

Instead, the schema models generalized cadence relationships:

### Quatrain cadence structure

| Line | Cadence    |
| ---- | ---------- |
| 1    | antecedent |
| 2    | antecedent |
| 3    | consequent |
| 4    | consequent |

### Couplet cadence structure

| Line | Cadence    |
| ---- | ---------- |
| 1    | antecedent |
| 2    | consequent |

The terms *cadence*, *antecedent*, and *consequent* were borrowed from music theory, where they describe tension and resolution relationships. Here they are adapted to model poetic rhyme function while leaving exact rhyme identity flexible.

This approach preserves:

* structural consistency
* reusable schema design
* Shakespearean variation
* semantic generality

without turning the schema into a rigid duplicate of a single sonnet instance.

---

## DTD and XSD

The project includes both DTD and XSD schema definitions.

### DTD

DTD provides a compact and highly readable way to define:

* permitted elements
* ordering rules
* repetition constraints
* required attributes

Its syntax is concise and expressive, particularly for document-oriented structures.

However, DTDs have limitations, especially when working with mixed content models and more advanced validation constraints.

### XSD

XSD expands XML validation into a more expressive schema language with:

* explicit typing
* richer constraints
* structured validation logic
* greater declarative precision

This additional expressive power comes at the cost of substantially greater verbosity.

The contrast between DTD and XSD highlights a broader tradeoff common in schema systems: compact readability versus formal expressiveness.

The purpose of this project is not to advocate XML schema technologies for modern production systems, but to examine them historically as examples of explicit structural modeling.

---

## Schema as Formal Design

Although DTD and XSD are not programming object systems, they require a similar kind of disciplined structural thinking:

* defining relationships
* constraining valid states
* modeling hierarchy
* making assumptions explicit
* separating valid from invalid structure

In that sense, the schemas function as declarative contracts for document structure independent of any rendering or processing system.

This mirrors broader concepts found in:

* database schema design
* API contracts
* type systems
* formal grammars

where clarity of structure matters more than implementation details.

The project therefore treats XML schema languages not merely as historical technologies, but as useful demonstrations of formal structural reasoning.

---

## Validation Workflow

Modern browsers no longer reliably perform DTD or XSD validation directly, so validation is performed externally using `xmllint` from the `libxml2` toolkit.

> [libxml2 project](https://gitlab.gnome.org/GNOME/libxml2?utm_source=chatgpt.com)

Validation is executed inside a lightweight Docker container to provide reproducible tooling independent of the host environment.

### Workflow

```text
XML document
   ↓
DTD/XSD validation
   ↓
CSS presentation
```

---

## CSS Rendering

The XML document is rendered directly in the browser using CSS rather than transformed into HTML.

This preserves the XML document itself as the primary artifact while maintaining complete separation between structure and presentation.

Several rendering decisions intentionally emulate historical printed typography:

* centered manuscript layout
* Renaissance-inspired typeface
* decorative drop cap
* muted analytical color coding
* preserved poetic line structure

### Rendering rhyme metadata

The XML markup includes semantic rhyme annotations:

* `scheme`
* `type`
* `cadence`

These attributes are rendered visually using CSS selectors:

| Attribute | Visual treatment |
| --------- | ---------------- |
| `scheme`  | muted color      |
| `type`    | italic/normal    |
| `cadence` | underline/normal |

The goal is analytical visibility without excessive visual clutter.

### Preserving poetic line structure

The CSS intentionally uses:

```css
white-space: nowrap;
```

to preserve poetic line integrity and emulate the appearance of a printed manuscript page.

Ordinarily this would conflict with responsive design expectations, but in this project the preserved line structure is itself semantically meaningful.

Importantly, this is purely a visual rendering choice. The underlying XML structure remains accessible and readable independent of CSS presentation.

---

## Historical Typography

The project uses the Google font *IM Fell DW Pica*, derived from seventeenth-century type associated with John Fell (1625–1686). Its irregular texture resembles early printed texts and reinforces the Renaissance manuscript aesthetic.

Decorative flourishes also make limited use of Unicode dingbat characters, demonstrating how modern Unicode can still support historically inspired typographic presentation.

---

## Result

The final system combines:

* semantic literary structure
* schema validation
* analytical metadata
* CSS visual rendering
* accessibility-preserving document order
* archival-inspired typography

using only:

* XML
* DTD/XSD
* CSS
* browser rendering
* xmllint

with almost no additional tooling.

In many ways, this represents a remarkably direct demonstration of the original XML document philosophy.

---

## Dockerfile

```text
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    libxml2-utils \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY src /app/src
```

## Docker set up

```bash
docker build -t sonnet-img .
```
```bash
docker run -d --name sonnet-container sonnet-img tail -f /dev/null
```
```bash
docker exec -it sonnet-container bash
```

---

## Validation

### XML 

```bash
head src/sonnet29.xml
```
```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/css" href="sonnet29.css"?>
<!DOCTYPE document SYSTEM "sonnet29.dtd">
<document>
  <sonnet>
    <title>Sonnet XXIX</title>
    <quatrain number="1">
      <line number="1">When in disgrace with fortune and men's e<rhymeA type="perfect" cadence="antecedent">yes</rhymeA>,</line>
      <line number="2">I all alone beweep my outcast st<rhymeB type="perfect" cadence="antecedent">ate</rhymeB>,</line>
      <line number="3">And trouble deaf heaven with my bootless cr<rhymeA type="perfect" cadence="consequent">ies</rhymeA>,</line>
```

### DTD

```bash
head src/sonnet29.dtd
```
```xml
<!ELEMENT document (sonnet+)>
	<!ELEMENT sonnet (title, quatrain, quatrain, quatrain, couplet, author)>
		<!ELEMENT title (#PCDATA)>
		<!ELEMENT quatrain (line, line, line, line)>
		<!ELEMENT couplet (line, line)>
			<!ELEMENT line (#PCDATA|rhyme)*>
				<!ELEMENT rhyme (#PCDATA)>
		<!ELEMENT author (name,date)>
			<!ELEMENT name (#PCDATA)>
			<!ELEMENT date (#PCDATA)>
```

### xmllint

```bash
xmllint --noout --dtdvalid src/sonnet29.dtd src/sonnet29.xml
```

> `xmllint` is silent. Check last exit code to verify it ran without error.

```bash
echo $?
```
```text
0
```

### XSD

```bash
head -30 src/sonnet29.xsd
```
```xsd
<?xml version="1.0" encoding="UTF-8"?>

<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <!-- ========================================================= -->
  <!-- DOCUMENT -->
  <!-- ========================================================= -->

  <xs:element name="document">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="sonnet" type="sonnetType" maxOccurs="unbounded"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>

  <!-- ========================================================= -->
  <!-- SONNET -->
  <!-- ========================================================= -->

  <xs:complexType name="sonnetType">
    <xs:sequence>

      <xs:element name="title" type="xs:string"/>

      <!-- exactly three quatrains -->

      <xs:element name="quatrain" type="quatrainType"/>
      <xs:element name="quatrain" type="quatrainType"/>
      <xs:element name="quatrain" type="quatrainType"/>
```

### xmllint

```bash
xmllint --noout --schema src/sonnet29.xsd src/sonnet29.xml
```
```text
src/sonnet29.xml validates
```

---

## CSS Presentation

### Browser 

> [Sonnet XXIX rendered with CSS in browser](https://mikecurtis1.github.io/Semantic_XML/src/sonnet29.xml)

### PNG for long term preservation

![PNG of CSS rendering](https://mikecurtis1.github.io/Semantic_XML/img/sonnet029.png)

### Comparison to 1609 first printing

![Image of 1609 printing](https://mikecurtis1.github.io/Semantic_XML/img/sonnet029_1609.png)
