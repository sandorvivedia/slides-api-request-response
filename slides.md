---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides, markdown enabled
title: API Standardization
info: |
  ## API Standardization
  Streamlining Requests & Responses

# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# API Standardization

## Streamlining Requests & Responses

<div class="abs-br m-6 flex gap-2">
        <a
            href="https://github.com/sandorvivedia/slides-api-request-response" target="_blank" alt="GitHub" title="Open in GitHub"
            class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white"
        >
             <carbon-logo-github />
    </a>
</div>

---

# JSON API

A SPECIFICATION FOR BUILDING APIS IN JSON

HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

```json
{
  "success": true,
  "message": "User logged in successfully",
  "data": {}
}
```

HTTP/1.1 422 Unprocessable Entity
Content-Type: application/vnd.api+json

```json
{
  "message": "The team name must be a string. (and 4 more errors)",
  "errors": {
    "team_name": [
      "The team name must be a string.",
      "The team name must be at least 1 characters."
    ],
    "authorization.role": ["The selected authorization.role is invalid."]
  }
}
```

<div class="abs-br m-6 flex gap-2">
        <a
            href="https://jsonapi.org/" target="_blank" alt="A SPECIFICATION FOR BUILDING APIS IN JSON" title="A SPECIFICATION FOR BUILDING APIS IN JSON"
            class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white"
        >
             <mdi-code-json />
    </a>
</div>

---
layout: cover
background: https://cover.sli.dev
---

# API response helper

Ensure consistent JSON API responses throughout an application.

<div class="abs-br m-6 flex gap-2">
  <a href="https://bitbucket.org/vivedia-ltd/laravel_api/pull-requests/1517" target="_blank" alt="Open PR in Bitbucket" title="Open PR in Bitbucket"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <mdi-bitbucket />
  </a>
</div>

---
layout: two-cols
layoutClass: gap-16
---

# Usage

```php {all|10,15-17}
<?php

namespace App\Http\Api\Controllers;
use Illuminate\Http\JsonResponse;

class OrdersController
{
    public function index(): JsonResponse
    {
        return $this->respondOk();
    }

    public function store(): JsonResponse
    {
        $resource = PostResource::make($post);

        return $this->respondCreated($resource);
    }

    ...
}
```

::right::

<div class="pt-12">
Returns a 200 HTTP status code

```json
{
  "message": "Success"
}
```

Returns a 201 HTTP status code, with response optional data

```json
{
  "id": 1234,
  "name": "John Smith"
}
```

...

</div>

---

# Available methods

- ```php
  respondNotFound(string|Exception $message, ?string $key = 'message')
  // Returns a 404 HTTP status code, an exception object can optionally be passed.
  ```

- ```php
  respondWithSuccess(array|Arrayable|JsonSerializable|null $contents = null)
  // Returns a 200 HTTP status code, optionally $contents to return as json can be passed.
  ```

- ```php
  respondOk(string $message)
  // Returns a 200 HTTP status code
  ```

- ```php
  respondUnAuthenticated(?string $message = null)
  // Returns a 401 HTTP status code
  ```

- ```php
  respondForbidden(?string $message = null)
  //Returns a 403 HTTP status code
  ```

- ```php
  respondError(?string $message = null)
  // Returns a 400 HTTP status code
  ```

- ```php
  respondCreated(array|Arrayable|JsonSerializable|null $data = null)
  // Returns a 201 HTTP status code, with response optional data
  ```

- ```php
  respondNoContent(array|Arrayable|JsonSerializable|null $data = null)
  // Returns a 204 HTTP status code, with optional response data.
  ```

---
layout: cover
background: https://cover.sli.dev
---

# Laravel Data

Powerful data objects for Laravel

```php
use Spatie\LaravelData\Data;

class SongData extends Data
{
    public function __construct(
        public string $title,
        public string $artist,
    ) {
    }
}
```

<div class="abs-br m-6 flex gap-2">
  <a href="https://spatie.be/docs/laravel-data/v4/getting-started/quickstart" target="_blank" alt="spatie/laravel-data" title="spatie/laravel-data"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---

## As a DTO

```php
#[TypeScript]
#[MapOutputName(CamelCaseMapper::class)]
class ServiceData extends Data
{
    #[Computed]
    public string $first_name;

    #[Computed]
    public string $last_name;

    public function __construct(
        public Optional|int $id,
        #[Max(10), Min(5)]
        public string $name,
        #[WithCast(DateTimeInterfaceCast::class)]
        #[WithTransformer(DateTimeInterfaceTransformer::class, format: 'd/m/Y H:i:s')]
        #[Date]
        public Carbon $time,
        public ?SiteData $site,
    ) {
        $this->first_name = Str::of($this->name)->before(' ')->value();
        $this->last_name = Str::of($this->name)->after(' ')->value();
    }
}
```

---

## Site Controller Index

````md magic-move
```php {*|10}
// step 1
class SiteController extends Controller
{
    public function index(IndexRequest $request): SiteCollection
    {
        $sites = Site::orderBy('siteName')
            ->search($request->string('search'))
            ->paginateIfRequested($request);

        return new SiteCollection($sites);
    }
}
```

```php {4,10-12|*}
// step 2
class SiteController extends Controller
{
    public function index(IndexRequest $request): DataCollection
    {
        $sites = Site::orderBy('siteName')
            ->search($request->string('search'))
            ->paginateIfRequested($request);

        return SiteData::collect($sites, DataCollection::class)
            ->wrap('sites')
            ->except('address');
    }
}
```
````

<!-- Footer -->

- [Data Collections](https://spatie.be/docs/laravel-data/v4/as-a-data-transfer-object/collections#content-datacollections-paginateddatacollections-and-cursorpaginatedcollections)
- [From data to resource](https://spatie.be/docs/laravel-data/v4/as-a-resource/from-data-to-resource)

---
layout: two-cols
layoutClass: gap-8
---

Site Data

```php {*|12}
namespace App\Data;

use Spatie\LaravelData\Data;
use Spatie\LaravelData\Optional;
use App\Data\AddressData;

class SiteData extends Data
{
    public function __construct(
        public Optional|int $id,
        public string $name,
        public AddressData $address,
        public bool $bacas,
        public string $crm,
        public string $knownAs,
        public null|int $maxPlaylistItems
    ) {
    }
}

```

[Address cast](https://bitbucket.org/vivedia-ltd/laravel_api/pull-requests/1526#chg-app/Casts/Address.php)

::right::

Address data

```php {none|*}

namespace App\Data;

use App\Casts\Address;
use Spatie\LaravelData\Data;

class AddressData extends Data
{
    public function __construct(
        public ?string $line1,
        public ?string $line2,
        public ?string $city,
        public ?string $postcode,
    ) {
    }

    public static function castUsing(array $arguments)
    {
        return Address::class;
    }
}
```

---

## Site Controller Store

````md magic-move
```php {*|4,6,8}
// step 1
class SiteController extends Controller
{
    public function store(SiteRequest $request, SiteResourceContract $response): SiteResourceContract
    {
        $site = $this->repo->create($request->validated());

        return $response::make($site);
    }
}
```

```php {4,6,8|*}
// step 2
class SiteController extends Controller
{
    public function store(SiteData $data): SiteData
    {
        $site = $this->repo->create($data->toArray());

        return SiteData::from($site);
    }
}
```
````

---

Side data as request

````md magic-move
```php {*}
class SiteData extends Data
{
    public function __construct(
        public Optional|int $id,
        public string $name,
        public AddressData $address,
        ...
    ) {
    }
}
```

```php {*}
class SiteData extends Data
{
    public function __construct(
        public Optional|int $id,
        public string $name,
        public AddressData $address,
        ...
    ) {
    }

    public static function rules(): array
    {
        return [
            'siteName' => 'required|string|max:' . Site::NAME_MAX_LENGTH,
            'siteAddress1' => 'required|string|max:' . Site::ADDRESS_MAX_LENGTH,
        ];
    }
}
```

```php {*}
class SiteData extends Data
{
    public function __construct(
        public Optional|int $id,
        public string $name,
        public AddressData $address,
        ...
    ) {
    }

    public static function rules(): array
    {
        return [
            'siteName' => 'required|string|max:' . Site::NAME_MAX_LENGTH,
            'siteAddress1' => 'required|string|max:' . Site::ADDRESS_MAX_LENGTH,
        ];
    }

    public static function fromRequest(Request $request): static{
        return new self(
            name: $request->siteName,
            address: new AddressData($request->siteAddress1, $request->siteAddress2, ...)
            ...
        )
    }
}
```

```php {*}
class SiteData extends Data
{
    public function __construct(
        public Optional|int $id,
        #[MapInputName('siteName'), Max(Site::NAME_MAX_LENGTH)]
        public string $name,
        public AddressData $address,
        ...
    ) {
    }
}
```
````

---

# Transforming to TypeScript

Annotate each data object that you want to transform to Typescript with a #[TypeScript] attribute.

```php {*|1}
#[TypeScript]
class SiteData extends Data
{
    public function __construct(
        public Optional|int $id,
        public string $name,
        public AddressData $address,
        ...
    ) {
    }
}
```

To generate the typescript file , run:

```sh
php artisan typescript:transform
```

---
layout: two-cols
layoutClass: gap-10
---

Generated output

```ts {*}
declare namespace App.Data {

  export type AddressData = {
    line1: string | null;
    line2: string | null;
    ...
  };

  export type ServiceData = {
    id?: number;
    name: string;
    site: App.Data.SiteData | null;
    ...
  };

  export type SiteData = {
    id?: number;
    name: string;
    address: App.Data.AddressData;
    ...
  };
}
```

::right::

Usage

```vue
<script setup>
import { useFetch } from '@vueuse/core';

import SiteData = App.Data.SiteData;

const { data } = useFetch<SiteData[]>('api/sites');
</script>

<template>
  <pre>{{ data }}</pre>
</template>
```

<div class="abs-br m-6 flex gap-2">
        <a
            href="https://github.com/fabien0102/ts-to-zod" target="_blank" alt="ts-to-zod" title="Generate Zod schemas (v3) from Typescript types/interfaces."
            class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white"
        >
             <carbon-logo-github />
    </a>
</div>
