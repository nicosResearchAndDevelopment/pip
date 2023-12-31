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

Despite the latter statements, **PIP** is willing to introduce examples (for hopefully better understanding) on given bindings and offers proposals, being discussed and clarified in the future.

This proposal does not break the rules of ODRL's `rightOperandReference`.

---

## Prologue

The **PIP**, literally speaking:

> A *Policy*, as subject of this story, computed by an agent, the Policy Decision Point (**PDP**, concept), needs some *Information* as input to given constraints (utilized by given rules) and gets it from a *Point*, the Policy Information Point (**PIP**, concept).

To be precise, three types of **PIP**'s can be identified:

### internal

### direct

### indirect

Even *internal* and *direct* are willing to work on proposed model (offered here), but the main addressee is the *indirect* Policy Information Point (**PIP**, concept, **PipSi**, Policy Information Point Service Instance).

This proposal has no preference to any transfer/transport protocol. Saying so, we have to understand, that given examples are shown to clarify and make it easy to use. All examples are aligned to all other bindings. Saying so, we are sure we are able to adopt any...

Using a commen protocol and data-model [PIP-DM] leads to faster and more robust implementations.

---

## What is the Information?

The Information is needed by a **PDP** (to enfore given Policy) and provided by a **PipSi**:

### Data Information

---

## Terminology

### PIP

The concept of Policy Information Point (**PIP**).

### PipSi

A *Policy Information Point Service Instance*. An instance of given class `pip:ServiceInstance`.

### pip

> TODO: Prefix `pip` of namespace URI "TODO:".

### PipCo

The **PIP** Client is - as a Consumer of information provided by **PipSi** (the Policy Information Provider as a Service Instance) - an instance of given class `pip:Client`, handing out retrieved information to **Pdp**, the main matter.

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

[**PIP-DM**] is assessable and is provided in turtle and json-ld.

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

The unit-model (like >qudt) to be used is out of scope of this (**PIP**) specification. Recommendations:

> TODO: unit recommendation

---

#### Durability

The content of given value (`pip:value`) is reliable ("best") for a period of time. Or, in a more restrictive definition: computation/<using></using> outside given period is dangerous, bad, not suitable.

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

Using `pip:validFrom` / `pip:validTo` might have a strong impact: given **PipSi** consumer, the Policy Information Point Consumer (**PipCo**) is able to cache given information.

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

## Request

Given Information Consumer (**PipCo**, acting in given **Pdp**) makes a request.

Example: PipCo of 'example2' requests information, introducing `pip:asset` and `pip:timestamp`:

```json
{
  "@id": "https://www.example2.com/information-request#ab-cd-ef",
  "@type": "pip:InformationRequest",
  "pip:asset": ".../temperature",
  "pip:timestamp": "2023-07-01T19:58:20.114Z",
  "pip:expectedUnit": "https://dbpedia.org/page/Celsius",
  "pip:expectedType": "xsd:decimal"
}
```

Using `pip:timestamp`: PipCo's point in time, sending this unique Information Request.

Using `pip:asset`: the temperature is adressed by given identifier and (possibly) not by PipSi's endpoint. Doing so, it reveals the opportunity to present the endpoint (PipSi) as an generic endpoint: all offered qualities (information) will be available at *one* PipSi-endpoint.

> TODO: TBC: POST recommended (**NOT** GET): request-parameters can be easily enriched for future mechanics!

PipSi's response, reusing PiCo's Information Request-Id (`pip:requestId`) and reusing `pip:asset`, the id of requested resource (here: the unique temperature-sensor):

```json
{
  "@id": "https://www.example.com/information#42-42-42-42-42-99",
  "@type": "pip:Information",
  "pip:requestId": "https://www.example2.com/information-request#ab-cd-ef",
  "pip:timestamp": "2023-07-01T19:58:20.121Z",
  "pip:asset": ".../temperature",
  "pip:validFrom": { "@type": "xsd:dateTime", "@value": "2023-07-01T19:58:20.121Z" },
  "pip:validUntil": { "@type": "xsd:dateTime", "@value": "2023-07-16T14:33:11.42Z" },
  "pip:unit": "https://dbpedia.org/page/Celsius",
  "pip:value": {
    "@type": "xsd:decimal",
    "@value": "42.24" 
  }
}
```

Using `pip:timestamp`: PipSi's point in time, responding with this unique Information (7 milliseconds after PipCo's Information Request).

## Self Description of PipSi

> TODO: 

---


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

[Example: data-typed request (Process)](./example/process/EXAMPLE.DATA-TYPED.REQUEST.md).

---

## Future

Ideas, next steps, clarifications.

### Object Type related Information

PIP's first-class-citizen is the data-typed value (`pip:value`), used on policy-constrain-level: constraints are working invariably on data-typed-imformation!

Introducing "Object Type related Information": given Policy Information Point Service Instance (**PipSi**) provides - objects. Those objects are (sometimes) needed on Pdp-side, not provided *internally* or *directly*, so this information is to be labeled as *indirect*.

> TODO: excurse on internal, direct and indirect

---

### Comstraint Request

The main idea here is, to expand the toolset of **PIP**. A Policy Information Point Service Instance is able to compute constraints, comparing two (data-typed) values, utilizing well-known operators.

Example: PipCo of 'example2' requests constrain-computation:

```json
{
  "@id": "https://www.example2.com/information-request#gh-ah-41-42",
  "@type": "pip:ConstraintRequest",
  "pip:timestamp": "2023-07-01T19:58:20.114Z",
  "pip:constraint": [
    {
	  "@type": "odrl:Constraint",
	  "pip:leftOperand": { "@type": "xsd:decimal", "@value": "41" },
	  "pip:operator": "odrl:lt",
      "pip:rightOperand": { "@type": "xsd:decimal", "@value": "42" }
    }
  ],
  "pip:expectedType": "xsd:boolean"
}
```

PipSi's response:

```json
{
  "@id": "https://www.example.com/information#42-42-42-42-42-99099",
  "@type": "pip:Information",
  "pip:requestId": "https://www.example2.com/information-request#gh-ah-41-42",
  "pip:timestamp": "2023-07-01T19:58:20.121Z",
  "pip:value": {
    "@type": "xsd:boolean",
    "@value": "true" 
  }
}
```

But, why doing so?

1. Because we can do it.
2. There are possibly some other reasons, given by **Pipsi**.

The admin-group-problem

Let us assume there is an admin-group and we want to know, if Alice is member of this group. Let us assume, there Is a PipSi, able to provide us with all members of given admin-group.

We have to keep in mind: the shown content of `odrl:rightOperandReference` was populated (de-referenced) at runtime. This means the Pdp makes a request (relation *indirect* not inside (*direct* relation) of running Pdp) to PipSi. The policy (in rest) shows:

```json
{
	"odrl:rightOperandReference": "https://www.example2.com/domain/group/admin?property=member"
}
```

Even this is **NOT** the mechanic (odrl de-referencing) of getting information from PipSi - the problem is the same (see in the following text)...

Reference resolved:

```json
{
	"odrl:dataType": "xsd:anyURI",
	"odrl:leftOperand": "https://www.example.com/employee/alice",
	"odrl:operator": "odrl:isAnyOf":
	"odrl:rightOperandReference": [
			"https://www.example.com/employee/bob",
			"https://www.example.com/employee/jim",
			"https://www.example.com/employee/fred",
			"https://www.example.com/employee/alice",
			"...AND 38 MORE"
	]
}
```

...on Pdp-side we will see: this is true, eberything is fine, Alice has har needed access to requested resource (it seems like given policy has somethig to do with *Access Control*...).

But now let us make an assumption: PipSi (working on behalf of some one...) is not willing to provide us (the Pdp) with this information of 42 members in given admin-group, because of some fuzzy privacy, security, safety or whatever reasons... the data owner does not reveal needed information!

*Constraint Information* will solve this problem:

```json
{
  "@id": "https://www.example2.com/information-request#gh-ah-98-98",
  "@type": "pip:ConstraintRequest",
  "pip:timestamp": "2023-07-01T19:58:20.114Z",
  "pip:constraint": [
    {
	  "@type": "odrl:Constraint",
	  "pip:leftOperand": { "@type": "xsd:anyURI", "@value": "https://www.example.com/employee/alice" },
	  "pip:operator": "xorg:memberOf",
      "pip:rightOperandReference": "https://www.example2.com/domain/group/admin"
    }
  ],
  "pip:expectedType": "xsd:boolean"
}
```

...leading to `true`. But where `xorg:memberOf` is coming from? Next question.

---

### Subscriptions

Describing a given Policy Information Point Consumer (**PipCo**) is able to subscribe information, so values can be cached at runtime (on **PDP**-agent-side) and will be replaced while Policy Information Point Service Instance (**PipSi**) is updating the needed information to gain improved performance and drop latency and band-width.

Sended Information (by given Information Provider, **PipSi**) should not be decorated with durability of provided value (`pip:value`). This seems natural: the provider updates when relevant. The consumer (**PipCo**) will recieve needed information with best quality and timley. Durability-decoration might be cause of errors: cached information might be outdated and so will be deleted, constraint (related to this information) is not working any more: information underrun. So, outdated information must be activily re-requested (as it has to be done by non-subscribed, regular requested data, too).

### Verifiable Information

Information provided by given *Policy Information Point Service Instance* (**PipSi**) in a verifiable fashion.

## References

### PROV

### Verifiable Credentials

---