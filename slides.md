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

# As a DTO

```php
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

---

# As a Resource

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

```php {4,10|*}
// step 2
class SiteController extends Controller
{
    public function index(IndexRequest $request): DataCollection
    {
        $sites = Site::orderBy('siteName')
            ->search($request->string('search'))
            ->paginateIfRequested($request);

        return SiteData::collect($sites, DataCollection::class)->wrap('sites');
    }
}
```
````

<!-- Footer -->

- [Data Collections](https://spatie.be/docs/laravel-data/v4/as-a-data-transfer-object/collections#content-datacollections-paginateddatacollections-and-cursorpaginatedcollections)
- [From data to resource](https://spatie.be/docs/laravel-data/v4/as-a-resource/from-data-to-resource)
