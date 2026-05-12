# Semantic XML

Introduction. 

Schema to validate semantic structure and CSS to render visually.

This project explores XML as both a semantic literary encoding system and a presentation-aware document model. Structural poetic relationships such as rhyme scheme, cadence, stanza organization, and typographic emphasis are represented directly in XML and selectively visualized through CSS attribute selectors.

## Why a Sonnet?

At first glance, a sonnet may seem like an unusual or even decorative choice for a structured XML example. However, its value lies in how it exposes a broader distinction in data modeling: the difference between structures that describe metadata about content and structures that participate in the embedded semantics of the content itself.

In many common XML domains—such as catalog data, user records, or even musical composition—the schema primarily serves a metadata role. It describes attributes of the subject: titles, identifiers, timestamps, categories, or organizational hierarchy. The structure is useful, but largely external to the meaning of the content it describes.

A sonnet, by contrast, is a tightly constrained literary form where structure is not merely descriptive but semantically active. The grouping into quatrains, the fixed line count, and even the rhyme structure are not incidental properties—they define what the artifact is. In this sense, the schema is not only describing the document; it is enforcing the rules that give the document its identity.

This makes the sonnet a useful example for exploring how XML schemas can operate at different levels of abstraction. It demonstrates a case where structural constraints are closely coupled to meaning, rather than existing solely as metadata annotations on top of otherwise unconstrained content.

Seen this way, the example is not about poetry as subject matter, but about structure itself: specifically, how far a schema can move from describing a document toward encoding the rules that generate its meaning.

## A Sonnet Schema

While the XML schema could theoretically enforce a rigid Shakespearean rhyme sequence such as `ABAB CDCD EFEF GG`, doing so would overconstrain the model and fail to reflect the more fluid rhyme relationships present within the poem itself (sonnet 29 has an actual rhyme scheme of `ABAB CDCD EBEB GG`.) The schema instead emphasizes "cadence" relationships (antecedent/consequent) - cadence, antecedent, and consequent are terms I borrowed from music theory, but represent well the actual attributes of rhyming in sonnet 29 - as a more generalized representation of poetic structural function while leaving specific rhyme identity more flexible.

Attempting to fully encode the Shakespearean rhyme sequence directly into XSD quickly causes the schema to become excessively instance-specific, effectively transforming the schema into a structural duplicate of the sonnet itself. The project therefore intentionally stops short of enforcing exact rhyme-order semantics in favor of maintaining a more generalized and reusable document model.

`ABAB CDCD EFEF GG ` is a formal abstraction, and ideal, but in actuality the formal design is more accurately... 

Quatrain

| Line | Cadence    |
| ---- | ---------- |
| 1    | antecedent |
| 2    | antecedent |
| 3    | consequent |
| 4    | consequent |

Couplet

| Line | Cadence    |
| ---- | ---------- |
| 1    | antecedent |
| 2    | consequent |

This allows for a schema that...

* rhyme scheme remains flexible
* cadence structure remains enforceable
* Shakespearean variation remains possible

Schema precision issues

Defining a `line` and methods for selecting the nth letter of a line.

> must begin with an optional `first_word` element, then arbitrary text, then must end with a `rhyme` element

DTD encoding versus reality

> ```xml
> <!ELEMENT line (first_word?, (#PCDATA), rhyme)>
> ```
>
> versus 
>
> ```xml
> <!ELEMENT line (#PCDATA|first_word|rhyme)*>
> ```

Known issue defining structural sequence with mixed content.

DTDs provided a compact and human-readable way to describe document structure, while XSD expanded XML validation into a far more expressive but substantially more verbose schema language. This project intentionally explores both approaches as examples of formal data modeling rather than as recommendations for contemporary production workflows.

## libxml2 and xmllint

> <https://gitlab.gnome.org/GNOME/libxml2>

With xmllint validating cleanly, you now have:

* well-formed XML
* structurally valid DTD constraints
* reproducible validation outside the browser
* a reliable baseline for the sonnet model

That means your schema is no longer just conceptual — it’s enforceable.

## Workflow

```text
XML document
   ↓
DTD/XSD validation
   ↓
CSS presentation
```

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

## Output

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

```bash
xmllint --noout --dtdvalid src/sonnet29.dtd src/sonnet29.xml
```
```bash
echo $?
```
```text
0
```

> `xmllint` is silent. Check last exit code to verify it ran without error.

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
```bash
head src/sonnet29.xml
```
```bash
xmllint --noout --schema src/sonnet29.xsd src/sonnet29.xml
```
```text
src/sonnet29.xml validates
```

---

## CSS 

Why CSS.

> The browser is a “renderer”, not a transformer.

Historic typography background.

Review `first_word` element drop cap sematic versus presentation concern.

`white-space: nowrap`, printed page emulation, responsive design, and accessibility. Normally, `white-space: nowrap` is hostile to responsive design because it fights: viewport adaptation, text reflow, accessibility expectations. But this CSS rendering presents a preserved literary artifact, a manuscript page, where the poetic line is a meaningful structural unit. A centered fixed-width manuscript block floating within the viewport. Viewing a preserved page. CSS `nowrap` is a _visual_ rendering option separate from the XML structure. In terms of accessibility the CSS is ignored while the XML presents no obstacle to screen readers.

Rendering rhyme. Including rhyme attributes `scheme` letter, `type`, and `cadence` provides rich detail beyond mere line enumeration and sequencing. It brings poetic analytical encoding to the markup. Visual rendering of these attributes therefore is not merely aesthetic, but should embody an analytical intent, and provide a visual aid to literary analysis. Three aspects needed to be viusalized without adding visual _clutter_. `Type` and `cadence` required only distinguishing two values while `scheme` distinguishes seven. Some text visualizations are inherently binary - underline/normal, italic/normal - while color offers a range of values. Therefore, `type` was rendered with `italic/normal`, `cadence` with `underline/normal` and the seven scheme letters with muted `color` choice.

Rendering quatrain and couplet.

Font face. Google font `IM Fell DW Pica` is based on an original font from John Fell, D.D. who died on 10 July 1686. It is an appropriate historical font for a Renaissance feel and has an irregular texture that mimics the appearance of historical printed texts. 

Flouret decoration. Unicdoe dingbats at first seem  anathema to both tasteful typography and semantic meaningfulness, but in fact certain historic flouret marks and included in the Unicode dingbat block.

### The especially elegant part of your project

You now have:

* semantic literary structure
* analytical metadata
* visual rendering rules
* accessibility-preserving document order
* archival aesthetics

all while using:

* plain XML
* CSS
* browser rendering

with almost no tooling.

That’s actually a remarkably pure demonstration of the original XML philosophy.

### Browser 

[Sonnet XXIX rendered with CSS in browser](https://mikecurtis1.github.io/Semantic_XML/src/sonnet29.xml)

### PNG for long term

![PNG of CSS rendering](https://mikecurtis1.github.io/Semantic_XML/img/sonnet029.png)

### Comparison to 1609 first printing

![Image of 1609 printing](https://mikecurtis1.github.io/Semantic_XML/img/sonnet029_1609.png)
