---
title: Validator Middleware
---

# Validator Middleware

Validator Middleware enables to validate query parameters, request headers, form body, and JSON body.
The point is declaring the "keys" which you will receive from the validate results.
Other values are ignored, so you will not receive any unexpected values.
And the validated values has "Types".

## Import

{{< tabs "import" >}}
{{< tab "npm" >}}

```ts
import { Hono } from 'hono'
import { validator } from 'hono/validator'
```

{{< /tab >}}
{{< tab "Deno" >}}

```ts
import { Hono } from 'https://deno.land/x/hono/mod.ts'
import { validator } from 'https://deno.land/x/hono/middleware.ts'
```

{{< /tab >}}
{{< /tabs >}}

## Usage

```ts
app.post(
  '/posts',
  validator((v) => ({
    // Validate header values specified with the key.
    // You can get validated value as `customHeader`.
    customHeader: v.header('x-custom').isAlpha(),
    // Validate JSON body.
    // You can get the values as structured data.
    post: {
      id: v.json('post.id').isRequired().asNumber(),
      title: v.json('post.title').isRequired().isLength({ max: 100 }),
      body: v.json('post.body').isOptional(),
    },
  })),
  (c) => {
    // Get validated data.
    const res = c.req.valid()
    const post = res.post
    return c.text(`Post: ${post.id} is ${post.title}`)
  }
)
```

You can validate queries, headers, body data, and JSON.

```ts
app.post(
  '/posts',
  validator((v) => ({
    page: v.query('page').isNumeric(),
    customHeader: v.header('x-custom').isRequired(),
    name: v.body('name').isOptional(),
    title: v.json('post.title').isLength({ max: 100 }),
  })),
  (c) => c.text('Valid')
)
```

Using `asNumber()` or `asBoolean()`, you can specify "type" for the value.

```ts
app.post(
  '/posts',
  validator((v) => ({
    id: v.json('post.id').asNumber().isRequired(),
    flag: v.json('post.flag').asBoolean().isRequired(),
  })),
  (c) => {
    const post = c.req.valid()
    const id = post.id // number
    const flag = post.flag // boolean
    return c.text(`${id} is ${flag}`)
  }
)
```

## Rules

For string:

- `isEmpty()`
- `isLength( options?: { max?: number, min?: number } )`
- `isAlpha()`
- `isNumeric()`
- `contains( elem, options?: { ignoreCase?: boolean, minOccurrences?: number })`
- `isIn( options?: string[] )`
- `match( regExp: RegExp )`

For number:

- `isGte( value: number, min: number )`
- `isLte( value: number, max: number )`

For boolean:

- `isTrue()`
- `isFalse()`