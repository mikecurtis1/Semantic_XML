# XML Sonnet

## Why a Sonnet?

At first glance, a sonnet may seem like an unusual or even decorative choice for a structured XML example. However, its value lies in how it exposes a broader distinction in data modeling: the difference between structures that describe metadata about content and structures that participate in the embedded semantics of the content itself.

In many common XML domains—such as catalog data, user records, or even musical composition—the schema primarily serves a metadata role. It describes attributes of the subject: titles, identifiers, timestamps, categories, or organizational hierarchy. The structure is useful, but largely external to the meaning of the content it describes.

A sonnet, by contrast, is a tightly constrained literary form where structure is not merely descriptive but semantically active. The grouping into quatrains, the fixed line count, and even the rhyme structure are not incidental properties—they define what the artifact is. In this sense, the schema is not only describing the document; it is enforcing the rules that give the document its identity.

This makes the sonnet a useful example for exploring how XML schemas can operate at different levels of abstraction. It demonstrates a case where structural constraints are closely coupled to meaning, rather than existing solely as metadata annotations on top of otherwise unconstrained content.

Seen this way, the example is not about poetry as subject matter, but about structure itself: specifically, how far a schema can move from describing a document toward encoding the rules that generate its meaning.

## Workflow

```text
XML document
   ↓
DTD/XSD validation
   ↓
CSS presentation
```

```bash
xmllint --noout --dtdvalid src/sonnet.dtd src/sonnet.xml
```

The browser is a “renderer”, not a transformer.



```bash
xmllint --noout --schema src/sonnet.xsd src/sonnet.xml
```

---

### Docker

```bash
docker build -t sonnet-img .
```
```bash
docker run -d --name sonnet-container sonnet-img tail -f /dev/null
```
```bash
docker exec -it sonnet-container bash
```

### Filesystem

```bash
pwd
```
```text
/app
```
```bash
ls -lah
```
```text
total 12K
drwxr-xr-x 1 root root 4.0K May 11 12:10 .
drwxr-xr-x 1 root root 4.0K May 11 12:13 ..
drwxr-xr-x 2 root root 4.0K May 11 12:07 src
```
```bash
ls -lah src
```
```text
total 24K
drwxr-xr-x 2 root root 4.0K May 11 12:07 .
drwxr-xr-x 1 root root 4.0K May 11 12:10 ..
-rw-r--r-- 1 root root 1.8K May 11 12:07 sonnet29.css
-rw-r--r-- 1 root root 1.2K May 11 12:07 sonnet29.dtd
-rw-r--r-- 1 root root 2.2K May 11 12:07 sonnet29.xml
-rw-r--r-- 1 root root 1.9K May 11 12:07 sonnet29.xsd
```
```bash
head src/sonnet29.xml
```
```text
<?xml version="1.0"?>
<?xml-stylesheet type="text/css" href="sonnet29.css"?>
<!DOCTYPE document SYSTEM "sonnet29.dtd">
<document>
  <sonnet>
        <title>Sonnet XXIX</title>
        <quatrain number="1">
                <line number="1"><first_word><first_letter>W</first_letter>hen</first_word> in disgrace with fortune and men's e<rhymeA type="perfect" cadence="antecedent">yes</rhymeA>,</line>
                <line number="2">I all alone beweep my outcast st<rhymeB type="perfect" cadence="antecedent">ate</rhymeB>,</line>
                <line number="3">And trouble deaf heaven with my bootless cr<rhymeA type="perfect" cadence="consequent">ies</rhymeA>,</line>
```
```bash
head src/sonnet29.dtd
```
```text
<!ELEMENT document (sonnet+)>
  <!ELEMENT sonnet (title,quatrain,quatrain,quatrain,couplet,author,hr)>
        <!ELEMENT title (#PCDATA)>
        <!ELEMENT quatrain (line,line,line,line)>
        <!ELEMENT couplet (line,line)>
                <!ELEMENT line ANY>
                        <!ELEMENT first_word (#PCDATA|first_letter)*>
                                <!ELEMENT first_letter (#PCDATA)>
                        <!ELEMENT rhymeA (#PCDATA)>
                        <!ELEMENT rhymeB (#PCDATA)>
```

### XML Lint

```bash
xmllint --noout --dtdvalid src/sonnet29.dtd src/sonnet29.xml
```
```bash
echo $?
```
```text
0
```


