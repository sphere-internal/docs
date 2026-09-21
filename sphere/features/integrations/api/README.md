---
description: >-
  Sphere provides an API which allows you to call our tax engine within custom
  billing flows.
icon: code
---

# API

## Overview

Sphere's API allows businesses to call our tax engine within their custom billing and checkout flows.&#x20;

We are also working on providing our API for third party software vendors to embed indirect tax compliance into their products. More to come on this soon!

## [​](https://docs.getsphere.com/api-reference/introduction#authentication)Authentication <a href="#authentication" id="authentication"></a>

The API endpoint requires authentication using an API key. To access the Sphere Tax Calculation API, you must include this API key in your request headers.

### Getting Your API Key

Please contact the Sphere team to obtain your unique API key. Once you have your key, you can include it in the header of each API request as shown below.

### Example Request Header <a href="#example-header" id="example-header"></a>

Include the API key in the `X-API-KEY` header:

```
X-API-KEY: YOUR_API_KEY
```
