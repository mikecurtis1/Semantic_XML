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

## Dockerfile

```text
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    libxml2-utils \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY src /app/src
```

## Docker

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

## xmllint

```bash
xmllint --noout --dtdvalid src/sonnet.dtd src/sonnet.xml
```

The browser is a “renderer”, not a transformer.


```bash
xmllint --noout --schema src/sonnet.xsd src/sonnet.xml
```

--- 

Schema precision issues

<!ELEMENT line (first_word?,(#PCDATA), rhyme)>

versus 

<!ELEMENT line (#PCDATA | first_word | rhyme)*>

```text
<!ELEMENT document (sonnet+)>
  <!ELEMENT sonnet (title, quatrain, quatrain, quatrain, couplet, author)>
	<!ELEMENT title (#PCDATA)>
	<!ELEMENT quatrain (line, line, line, line)>
	<!ELEMENT couplet (line, line)>
		<!ELEMENT line (#PCDATA|first_word|rhyme)*>
			<!ELEMENT first_word (#PCDATA)>
			<!ELEMENT rhyme (#PCDATA)>
	<!ELEMENT author (name,date)>
		<!ELEMENT name (#PCDATA)>
		<!ELEMENT date (#PCDATA)>
<!ATTLIST quatrain number (1|2|3) #REQUIRED>
<!ATTLIST line number (1|2|3|4) #REQUIRED>
<!ATTLIST rhyme scheme (A|B|C|D|E|F|G) #REQUIRED type (perfect|near) #REQUIRED cadence (antecedent|consequent) #REQUIRED>
```
