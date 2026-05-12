# Semantic XML

Introduction. Originally meant as a demonstration piece, the promise of XML to allow pure structural and semantic markup with full separation of visual rendering using CSS style sheets. The document is not metadata, which may need _tranformation_ (for example to dynamically transform metadata into hyperlinks), but is rather pure content with XML annotating structural and semantic features of the text.

Schema to validate semantic structure and CSS to render visually.

This project explores XML as both a semantic literary encoding system and a presentation-aware document model. Structural poetic relationships such as rhyme scheme, cadence, stanza organization, and typographic emphasis are represented directly in XML and selectively visualized through CSS attribute selectors.

One of the secondary goals of this project is to treat DTD and XSD not simply as legacy XML technologies, but as exercises in explicit structural design. Although they are not programming object models, they require a similar kind of disciplined thinking: defining relationships between elements, constraining valid structure, and making assumptions about data explicit before implementation.

In that sense, the schema definitions function as a formal contract for the shape of the data, independent of any particular processing system or output format. This mirrors broader design concerns found in database schema design, API contracts, and type systems, where clarity of structure is more important than the mechanics of implementation.

The intent here is not to modernize XML into something it is not, but to use it as a concrete example of how structured thinking about data can be expressed in multiple formalisms, each with different strengths and historical contexts.

DTD/XSD express structure as a declarative contract rather than executable logic.

## Why a Sonnet?

At first glance, a sonnet may seem like an unusual or even decorative choice for a structured XML example. However, its value lies in how it exposes a broader distinction in data modeling: the difference between structures that describe metadata about content and structures that participate in the embedded semantics of the content itself.

In many common XML domains—such as catalog data, user records, or even musical composition—the schema primarily serves a metadata role. It describes attributes of the subject: titles, identifiers, timestamps, categories, or organizational hierarchy. The structure is useful, but largely external to the meaning of the content it describes.

A sonnet, by contrast, is a tightly constrained literary form where structure is not merely descriptive but semantically active. The grouping into quatrains, the fixed line count, and even the rhyme structure are not incidental properties—they define what the artifact is. In this sense, the schema is not only describing the document; it is enforcing the rules that give the document its identity.

This makes the sonnet a useful example for exploring how XML schemas can operate at different levels of abstraction. It demonstrates a case where structural constraints are closely coupled to meaning, rather than existing solely as metadata annotations on top of otherwise unconstrained content.

Seen this way, the example is not about poetry as subject matter, but about structure itself: specifically, how far a schema can move from describing a document toward encoding the rules that generate its meaning.

## A Sonnet Schema

Formal structural specification of a literary form

* exactly 4 quatrains
* exactly 2 lines per couplet
* ordered structure throughout
* required metadata everywhere

The schema isn’t just “modeling metadata,” it’s modeling:

* structure
* hierarchy
* repetition rules
* bounded length
* grouped semantics (quatrain vs couplet)

Tight semantic schema (sonnet)

* fixed structure
* bounded length
* direct mapping to content units
* easy validation
* high determinism

The sonnet schema operates at a level where structural constraints closely track the smallest meaningful units of the content itself.

Sonnet schema is a constrained semantic form where structure encodes interpretation. XML schema validation ensures valid form expression.

* missing quatrain → meaningful failure
* broken rhyme scheme → structural violation
* incorrect grouping → semantic inconsistency

This project also highlights a broader distinction in structured data modeling: some schemas primarily describe metadata about a domain (such as musical composition structures), while others—like the sonnet model—encode structural rules that directly participate in the meaning of the underlying content.

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

### DTD 

A DTD defines rules like:

* what elements are allowed
* what order they must appear in
* what attributes exist and whether they’re required
* what can be repeated (*, +, ?)

Known issue defining structural sequence with _mixed_ content.

### XSD

* structured model with constraints
* explicit typing
* more declarative precision

DTDs provided a compact and human-readable way to describe document structure, while XSD expanded XML validation into a far more expressive but substantially more verbose schema language. This project intentionally explores both approaches as examples of formal data modeling rather than as recommendations for contemporary production workflows.

## libxml2 and xmllint

In the past web browsers could validate XML documents with declared DTD or XSD schema, but that function is now deprecated or already removed. But, the command line tool `xmllint` can be used instead for XML schema vaidation.

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
