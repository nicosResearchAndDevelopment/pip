# Policy Information Point (**PIP**, v2)

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

> > > **CHANGES (2023-08-08) to last contribution**

> > > **CHANGES to last contribution**

## Editors

- Jörg Langkau (nicos AG}

## Abstract

This document describes the architecture of a *Policy Information Point* (**PIP**, as concept) and given *PIP Data Model* [**PIP-DM**].

## Table of Content

- [Prologue](#prologue)
- [The P-Family](#the-p-family)
- [Introduction of data-typed Information](#introduction-of-data-typed-information)
- [Request for Information](#request-for-information)
- [Introducing `pip:Result`-Request](#introducing-pipinformation--request)
- [Service Description](#service-description)
- [Implementation / Binding](#implementation--binding)
- [Request, Cache and Subscription](#request-cache-and-subscription)

*Table of content 'Policy Information Point'*.

## Prologue

The **PIP**, literally speaking:

> A *Policy*, computed by an agent, the Policy Decision Point (**PDP**), needs some
*Information* as input to given constraints (utilized by given rules) and gets it, the *Information* from a
*Point*, the Policy Information Point (**PIP**).

## The P-Family

![image](./resources/p-family-2.png)

Two categories of **PIP**s can be identified:

### internal PIP

The PDP has direct access to given information, like 'current time'.

### external PIP

The PDP has indirect access to given information. So the decision utilises an external endpoint as a service. This service behaves as a Data Provider.

### Subject and Context

![image](./resources/context.png)

*Coming from [here](https://github.com/nicosResearchAndDevelopment/LDS/tree/main/misc/context).*

## Introduction of data-typed Information

Definition fo `dataType` in ODRL, based on `xsd:`:

``` json
{
	"dataType": {"@type": "xsd:anyType", "@id": "odrl:datatype"}
}
```
...taken from: [https://www.w3.org/ns/odrl.jsonld](https://www.w3.org/ns/odrl.jsonld)

The data-typed information (subjects *attribute* or *property*) is needed to be compared by binary operators:

Example: A constraint, expressed in ODRL. This expression will be computed (by PDP) **without external information** provided, all information is nested in given *inner context*, The Policy - and leads to `"true"`.

```json
{
	"@type": "odrl:Constraint",
	"odrl:leftOperand": { "@type": "xsd:decimal", "@value": "42.0" },
	"odrl:operator": "odrl:gt",
	"odrl:rightOperand": { "@type": "xsd:decimal", "@value": "36.5" },
	"odrl:dataType": "xsd:decimal"
}
```
> POINT TO: `odrl:dataType` is *ruled* by rightOperands `@type`! 

Example: A constraint, expressed in ODRL. This expression uses the [`odrl:rightOperandReference`](https://www.w3.org/TR/odrl-vocab/#term-rightOperandReference).

> ["A reference to a web resource providing the value for the right operand of a Constraint."](https://www.w3.org/TR/odrl-vocab/#term-rightOperandReference)

and

> ["An IRI that MUST be dereferenced to obtain the actual right operand value."](https://www.w3.org/TR/odrl-vocab/#term-rightOperandReference)

```json
{
	"@type": "odrl:Constraint",
	"odrl:leftOperand": { "@type": "xsd:decimal", "@value": "42.0" },
	"odrl:operator": "odrl:gt",
	"odrl:rightOperandReference": { "@type": "xsd:anyURI", "@value": "https://www.example.com/weather/station/muenster/temperature" },
	"odrl:dataType": "xsd:decimal",
	"odrl:unit": "http://qudt.org/vocab/unit/DEG_C"
}
```
> POINT TO: [`odrl:unit`](https://www.w3.org/TR/odrl-vocab/#term-unit)!


...shortcut 

```json
{
	"odrl:rightOperandReference": "https://www.example.com/weather/station/muenster/temperature"
}
```

---

## Request for Information

Request of Information from given Information Point **PIP**.

> data-typed *Attribute*, *Property*.
> 
> >NOT
> 
> *Resource*

Example: **PDP** (or better: given **context handler**) de-references a URI that is *hopefully* a URL:

```json
{
	"odrl:rightOperandReference": "https://www.nicos-ag.com/weather/station/muenster/temperature"
}
```

http request:
```http request
GET https://www.nicos-ag.com/weather/station/muenster/temperature
```
...response
```text
36
```

## Introducing `pip:Result`-Request

POST wins, because of better *parametrization*:

### 1. Request: the temperature from given Weather-station:

http request:

```http request
POST https://www.nicos-ag.com/weather/station/muenster/temperature

{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v2/",
	"@id": "https://www.example-2.com/request/1243-124-1234-1234-1234",
	"@type": "pip:Request",
	"pip:expectedType": [ "xsd:decimal", "xsd:integer" ],
	"pip:expectedUnit": "http://qudt.org/vocab/unit/DEG_C"
}
```
> POINT TO: `pip:expectedType` as array!

...response (remark: valid for 5 minutes):

```json
{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v2/",
	"@id": "https://www.nicos-ag.com/information/42-42-42-42-42"
	"@type": "pip:Result",
	"pip:requestId": "https://www.example-2.com/requast/1243-124-1234-1234-1234",
	"pip:requestedAt": "2023-07-17T12:00:01.042Z",
	"pip:validFrom": "2023-07-17T12:00:01.042Z",
	"pip:validTo": "2023-07-17T12:05:00.042Z",
	"pip:unit": "https://dbpedia.org/page/Celsius",
	"pip:value": { "@type": "xsd:decimal", "@value": "36.32" },
  	"pip:termsOfUse": { "@type": "xsd:anyURI", "@value": "https://www.nicos-ag.com/policy/for-the-secret-ones/13-13-13"}
}
```

> POINT TO: `pip:unit` ...not what expected!!!
> 
> POINT TO: unit conversion

### 2. Request: member of a group

Example: a constraint answering if someone is member of a group:

```json
{
	"@type": "odrl:Constraint",
	"odrl:leftOperand": { "@type": "xsd:anyURI", "@value": "https://www.nicos-ag.com/domain/user/juanjo" },
	"odrl:operator": "odrl:isPartOf",
	"odrl:rightOperandReference": { "@type": "xsd:anyURI", "@value": "https://www.nicos-ag.com/domain/group/admin/member" },
	"odrl:dataType": "xsd:anyURI"
}
```
> POINT TO: "odrl:dataType": "xsd:anyURI" :: data-type of given result!

http request:

```http request
POST https://www.nicos-ag.com/pip/
{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v2/",
	"@id": "https://www.example-2.com/request/6767-7667-67-67-67-67",
	"@type": "pip:Request",
	"pip:target": "https://www.nicos-ag.com/domain/group/admin/member",
	"pip:expectedType": "xsd:anyURI",
}
```
> POINT TO: `pip:target` ...so the POST-request is maybe a little different?!? >>>

...response:

```json
{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v2/",
	"@id": "https://www.nicos-ag.com/information/54-54-54-45-5-454"
	"@type": "pip:Result",
	"pip:requestId": "https://www.example-2.com/request/6767-7667-67-67-67-67",
	"pip:requestedAt": "2023-08-17T12:00:01.042Z",
	"pip:validFrom": "2023-08-17T12:00:01.042Z",
	"pip:validTo": "2023-08-17T12:05:00.042Z",
	"pip:value": { "@type": "xsd:anyURI", "@value": [
		"https://www.nicos-ag.com/domain/user/joerg",
		"https://www.nicos-ag.com/domain/user/juanjo",
		"https://www.nicos-ag.com/domain/user/jesus"
	] }
}
```

> POINT TO: problem of privacy/security
>
> > So: if the PIP does **NOT** show all members, we have to introduce a new PIP-Feature: resolving constraints...

---

### object-typed Information

> *Resource*, *Asset*, *Thing*.

This kind is used to resolve Collections, (ODRL) Asset or Party Collections, but is ''NOT** focussed by PIP-v2, now.

---

## Service Description

http request:

```http request
GET https://www.nicos-ag.com/pip/
```
...response:

```json
{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v2/",
	"@type": "pip:ServiceDescription",
	"pip:requestedAt": "2023-08-17T12:00:01.042Z"
}
```

> POINT TO: introducing : [rdfs:subClassOf  lds:ServiceProfile ;](https://github.com/nicosResearchAndDevelopment/LDS/blob/main/model/service/lds.service.profile.ttl)

---

## Authorisation

Authorisation is intentional left open and defined by given mechanics, revealed by **Pip**, or *ecosystem* acting in.

---

## Implementation / Binding

### http

:-(

### websocket

Opened socket!

### http3

Opened socket!

### gRPC

- Opened socket! (bi-directional streams!)
- PIP-DM `protobuf`-compatible

## Request, Cache and Subscription

- Caching received data for tagged period of time (see: `pip:validTo`).
- Subscription: cached information is the "freshest" possible. PIP "knows" and controls information-lifecycle.

---

## Future

`pip:Result`, `pip:InformationProcess` and `pip:Request` are already "prov-flavoured".

> POINT TO: traceability of data-flow...

---