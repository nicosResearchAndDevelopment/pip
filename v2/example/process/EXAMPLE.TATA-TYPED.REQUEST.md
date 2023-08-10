# Data-typed Request

## Information Request

*Policy Information Point Consumer* (**PipCo**) requests:

### Preparation on Consumer-side

### The Request Body

```json
{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v1/",
	"@id": "https://www.example.com/information/request#42-42-42-42-42"
	"@type": "pip:Request",
	"pip:expectedType": "xsd:decimal",
	"pip:expectedUnit": "https://dbpedia.org/page/Celsius"
}
```

```http request
POST "https://www.example2.com/weather/station/nrw/muenster/ws-42/temperature"
// Content :: "@id": "https://www.example.com/information/request#42-42-42-42-42"
```

## Information

### Preparation on Provider-side

### The Response Body

Requested *Policy Information Point Service Instance* (**PipSi**) responses with:

Remark: valid for 5 minutes.

```json
{
	"@context": "https://github.com/nicosResearchAndDevelopment/pip/tree/main/v1/",
	"@id": "https://www.example2.com/information/42-42-42-42-42"
	"@type": "pip:Result",
	"pip:requestId": "https://www.example.com/information/request#42-42-42-42-42",
	"pip:requestedAt": "2023-07-17T12:00:01.042Z",
	"pip:validFrom": "2023-07-17T12:00:01.042Z",
	"pip:validTo": "2023-07-17T12:05:00.042Z",
	"pip:unit": "https://dbpedia.org/page/Celsius",
	"pip:value": { "@type": "xsd:decimal", "@value": "42" }
}
```

---

## Information on Consumer-side

The consumer (**PipCo**) caches given information for the next 5 Minutes.

---