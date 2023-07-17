# Policy Information Point (**PIP**, v1)

> > > **CAUTION**
>
> - current status is something like a **draft** or starting point for **discussion**
> - current text is sometimes redundant and will be condensed by further work
> - all definitions sometimes are a *little* underspecified* :-(
> - all specification might contain *bugs*, that will be eliminated in future :-)
> - ...hopefully being a good starting point
> - so far **NOT** usable for production!
>
> > > **CAUTION**

> > > **CHANGES (2023-07-17) to last contribution**

> > > **CHANGES to last contribution**

J. Langkau

---

## Editors

- Jörg Langkau (nicos AG}

## Contributors

---

## Abstract

This document describes the architecture of a Policy Information Point (**PIP**, as concept) and given PIP Data Model [*
*PIP-DM**].

---

## Table of Content

- [Introduction](#introduction)
- [Conventions](#conventions)
- [Protocol](#protocol)
	- [Data Type related](#data-type-related)
	- [Object Type related](#object-type-related)
- [Server](#server)
- [Client](#client)
- [Authorisation](#authorisation]
- [Binding](#binding)
	- [http](#http)
	- [http2](#http2)
	- [gRPC](#grpc)
	- [websocket](#websocket)
	- [idscp2](#idscp2)
	- [tls](#tls)
- [Process](#Process)
- [References](#references)

*Table of content 'Policy Information Point'*.

---

## Conventions

- prefix: `pip`

> TODO: - namspace uri: https://

---

## Introduction

The Policy Information Point (**PIP
**), as a concept), as member of the ["Policy-Family"](https://github.com/nicosResearchAndDevelopment/Policy-Family):

- Policy Enforcement Point (**PEP**), starting point, targeted by given request.
- Policy Decision Point (**PDP**), computing given rules and constrains to express formulated rights.
- Policy Execution Point (**PXP**), executing given actions based on evaluated rights.
- Policy Administration Point (**PAP**), managing given policies.
- Policy Repository Point (**PRP**), persisting given policies.

...behaves in the end as a Service Instance **Pip**. At runtime **Pip** will be requested for some information by a Consumer **PipCo**, being a part of up and running **Pdp**: received *information* will be used - *MUST* be used to yield needed decisions or expressions, typically used for controlling the access over a resource (Access Control) or controlling the usage of a resource (Usage Control).

Despite posed simplicity - even the smallest piece of information, only one tiny value needed by a **Pdp**, accessed by its **PipCo**, fetching data (the *information*) from a distant **Pip**, has a huge impact: is this *information* not accessible or - even bad - not correct or reliable, given **Pdp** is **NOT** able to produce needed conclusion. Once again: one tiny value, a *boolean* only. This unalterable fact unfolds the importance of **PIP** (here again quoted as a concept) - if needed.

The concept of **PIP**

- is not - except for *linked-data*, *semantic web* - related to any technology or existing implementation (of **PEP**, for example), aiming a broader audience.
- and here, its main topic, *The Information* is underlain with provenance-tracking features, utilising [PROV](#prov].
- leaves the process of authorisation open, intentional.
- separates its own [Protocol](#protocol) and [Binding](#binding)s to - those done by more or less well-known transfer/transport protocols.
- is focused on its main topic: *The Information*, useful, hardened data.
- is prepared for [*Verifiable Information*](#verifiable-information), aligned to W3C's *Verifiable
  Credentials* [VC](#verifiable-credential).

Despite the latter statements, **PIP** is willing to introduce examples (for hopefully better understanding) on given bindings and offers proposals, to be discussed and clarified in the future.

---

## Prologue

The **PIP**, literally speaking:

> A *Policy*, as subject of this story, computed by an agent, the Policy Decision Point (**PDP**), needs some *Information* as input to given constraints (utilized by given rules) and gets it from a *Point*, the Policy Information Point (**PIP**).

To be precise, three types of **PIP**s can be identified:

### internal

### direct

### indirect

Even *internal* and *direct* are willing to work on proposed model (offered here), but the main addressee is the *indirect* Policy Information Point.

This proposal has no preference to any transfer/transport protocol. Saying so, we have to understand, that given examples are shown to clarify and make it easy to use. All examples are all aligned to all other bindings. Saying so, we are sure we are able to adopt any...

Using a commen protocol and data-model leads to faster and robust implementation.

---

## What is the Information?

The Information needed by a **PDP** (and Policy) and provided by a **PIP**:

### Data Information

### Object Information

### Future: Boolean Information

---

## Terminology

### PIP

The concept of Policy Information Point (**PIP**).

### Pip

A Policy Information Point Service Instance. An instance of given class `pip:ServiceInstance`.

### pip

> TODO: Prefix `pip` of namespace URI "TODO:".

### PipCo

The **PIP** Client is - as a Consumer of given **Pip** (the Policy Information Provider as a Service Instance) - an instance of given class `pip:Client`, handing out retrieved information to **Pdp**, the main matter.

### PDP

The concept of Policy Decision Point (**PDP**).

### Pdp

A Policy Decision Point Instance, as an agent used by application's Policy Enforcement Point (**Pep**, based on given concept **PEP**, for the sake of completeness).

### PEP

The concept of Policy Enforcement Point (**PEP**).

## Information

Useful data, presented to given recipient in a understandable and usable pattern.

## Data Model

**PIP**'s Data Model [**PIP-DM**] intends **NOT** - as strict as possible - to cover elements of used models, like [PROV](#prov), for instance.

> Don't reinvent the wheel!

...an often heard recommendation and **PIP** tries to follow.

Vocabulary is unfolded [TODO: here](./model/VOCABULARY.md).

[PIP-DM] is assessable and is provided in turtle and json-ld.

- [PIP-DM, turtle](./model/pip.ttl)

> TODO: json-ld REM: will be done, if ttl is finalized

### Information

Example (json): Information-node `pip:Information`:

```json
{
  "@context": [
    "https://github.com/nicosResearchAndDevelopment/pip/v1/
  ],
  "@id": "https://www.example.com/information#42-42-42-42-42-42",
  "@type": "pip:Information"
}
```

---

### Metadata

#### Unit

Units hold by `pip:unit` are only used for *data-typed* values.

Example (json): Information-node with declared unit `pip:unit` and a data-typed [`pip:value`](#data-typed-value):

```json
{
  "@id": "https://www.example.com/information#42-42-42-42-42-43",
  "@type": "pip:Information",
  "pip:unit": "https://dbpedia.org/page/Celsius",
  "pip:value": {
    "@type": "xsd:decimal",
    "@value": "42" 
  }
}
```

The unit-model to be used is out of scope of this (**PIP**) specification. Recommendations:

> TODO: unit recommendation

---

#### Durability

The content of given value (`pip:value`) is reliable ("best") for a period of time. Or, in a more restrictive definition: computed/used outside given period is dangerous, bad, not suitable.

Example (json): Information-node valid from/to (`pip:validFrom`, `pip:validTo`) and a data-typed [`pip:value`](#data-typed-value):

```json
{
  "@id": "https://www.example.com/information#42-42-42-42-42-44",
  "@type": "pip:Information",
  "pip:validFrom": { "@type": "xsd:dateTime", "@value": "2023-07-01T19:56:20.114Z" },
  "pip:validTo": { "@type": "xsd:dateTime", "@value": "2023-07-16T14:33:11.42Z" },
  "pip:unit": "https://dbpedia.org/page/Celsius",
  "pip:value": {
    "@type": "xsd:decimal",
    "@value": "42" 
  }
}
```

Using `pip:validFrom` / `pip:validTo` might have a strong impact: given **PipSi`` consumer, the Policy Information Point Consumer (**PipCo**) is able to cache given information.

Focussing on `pip:validTo` **PipCo** might decide to *cache* given information (here: content of `pip:value`) until it is expired. Doing so **PipCo** (to be more precise, the working agent **Pdp** utilizing cached value) is able to omit further requests up to stated point in time.

---

#### Terms Of Use

The content of given value (`pip:value`) is usable under expressed terms (formulated by entity given **PipSi** (Policy Information Point Service Instance) is acting behalf of.

Example (json): Information-node exposes *terms of use* (`pip:termsOfUse`) on given value `pip:value`:

```json
{
  "@id": "https://www.example.com/information#42-42-42-42-42-44",
  "@type": "pip:Information",
  "pip:validFrom": { "@type": "xsd:dateTime", "@value": "2023-07-01T19:56:20.114Z" },
  "pip:validUntil": { "@type": "xsd:dateTime", "@value": "2023-07-16T14:33:11.42Z" },
  "pip:unit": "https://dbpedia.org/page/Celsius",
  "pip:value": {
    "@type": "xsd:decimal",
    "@value": "42" 
  },
  "pip:termsOfUse": { "@type": "xsd:anyURI", "@value": "https://www.example.com/policy/for-the-secret-ones/13-13-13"}
}
```

---

### Value

Property: `pip:value`

The content of this, hold by `pip:value`, is the heart of the matter. Given information is needed and computed by up-and-running **Pdp** (agent).

We have to distinguish two flavors of **Value**'s content: *data-typed* and *object*.

#### data-typed Value

Data-typed Information, the content if `pip:value`, is presented by given data-type and the main value:

Example (json): data-typed and *single-valued* `pip:value`, a string:

```json
{
  "pip:value": {
    "@type": "xsd:string",
    "@value": "example" 
  }
}
```

Example (json): data-typed and *single-valued* `pip:value`, a number:

```json
{
  "pip:value": {
    "@type": "xsd:decimal",
    "@value": "42" 
  }
}
```

Example (json): data-typed and *multi-valued* `pip:value`, URIs:

```json
{
  "pip:value": {
    "@type": "xsd:anyURI",
    "@value": [
      "https://www.example.com/asset/this/",
      "https://www.example.com/asset/that/"
    ] 
  }
}
```

#### object Value

---

##                   

## Object Type related Information

## Service

### Profile

### Instance

The Policy Information Point Service Instance (**PipSi**) behaves as an up-and-running application, providing information and acting *on behalf of* well-known agent. PipSi refers to given PIP Service Profile.

## Client

The Policy Information Point Client (**PipCo**) as given *Information* Consumer resides inside a **PDP** and requests Information, especially the nested [Value](#value) utilized by Constraint.

Even this proposal left authorisation open, it is worth to mention, that in most of the case there will be one, eco-system/domain dependend.

---

## Authorisation

Authorisation is intentional left open and defined by given mechanics, revealed by **Pip**, or *eco-system* acting in.

## Binding

Whether or not a given binding is secure (execpt idscp2 or tls), those bindings will provide *methods* and work on top of given protocol *functions*.

This proposal will focus on two bindings (in the beginning): *http* and *websocket*. The former because it is easy to understand and widely spread, the latter because of its nature: facilitating realtime-performance on permanent connection, anticipating faster computation/Policy Enforcements.

Understanding the usage of websocket here will guide to other bindings, like tls, http2 or gRPC, that will be introduced in future extensions of this proposal.

### http

#### Data Type related

#### Object Type related

### http2

### gRPC

### websocket

### idscp2

### tls

## Process

The starting point is the *Information Request*:

### Information Request

The *Information Request* (`pip:InformationRequest`) is initiated by *Policy Information Point Consumer* (**PipCo**), sending the request to given *Policy Information Point Service Instance* (**PipSi**), the data-provider. At **PipSi**-side the *Information Process* (`pip:InformationProcess`) is triggered by **PipCo**'s *Information Request* as an (prov) Entity.

### Information Process

**PipSi's** *Information Process* (`pip:InformationProcess`), triggered by **PipCo**'s *Information Request* (`pip:InformationRequest`), instantiates the *informing* (prov) Activity.

### Informing Activity

The (prov) Activity "informing", harvests needed information and expose it on generated information (prov) Entity (`pip:Information`).

### Information

The Information (a prov Entity, an instance of `pip:Information`) is the product of the *Informing Activity* as an instance of *Information Process* (`pip:InformationProcess`), delivered to requesting **PipCo** by finalizing response to given *Information Request*.

### Example

[Example data-typed request](./example/process/EXAMPLE.TATA-TYPED.REQUEST.md).)

---

## Future

Ideas, next steps, clarifications.

### Subscriptions

Describing a given Policy Information Point Consumer (**PipCo**) is able to subscribe information, so values can be cached at runtime (on **PDP**-agent-side) and will be replaced while Policy Information Point Service Instance (**PipSi**) is updating the needed information to gain improved performance and drop latency and band-width.

### Verifiable Information

Information provided by given *Policy Information Point Service Instance* (**PipSi**) in a verifiable fashion.

## References

### PROV

### Verifiable Credentials

---