# Including Relationships

Dynamically including related resources in API responses.

## Query Parameter

Clients request includes via query parameter:

```
GET /users?include=posts
```

## Resource Implementation

```php
// Allow includes
class UserResource extends JsonResource
{
    public function toArray($request): array
    {
        $includes = $request->query('include', '');
        
        return [
            'id' => $this->id,
            'attributes' => [
                'name' => $this->name,
                'email' => $this->email,
                // ...
            ],
            'include' => $this->when(
                str_contains($includes, 'posts'),
                fn () => PostResource::collection($this->posts)
            ),
        ];
    }
}
```

## Conditional Relationships

Use `when()` to conditionally include:

```php
$this->when(
    str_contains($includes, 'posts'),
    fn () => PostResource::collection($this->posts)
)
```

## Multiple Includes

```php
$includes = $request->query('include', '');

return [
    'id' => $this->id,
    'attributes' => [...],
    'include' => collect([
        $this->when(
            str_contains($includes, 'posts'),
            'posts' => PostResource::collection($this->posts)
        ),
        $this->when(
            str_contains($includes, 'comments'),
            'comments' => CommentResource::collection($this->comments)
        ),
    ])->filter(),
];
```

## Validation

Only allow safe includes to prevent N+1 queries:

```php
$allowedIncludes = ['posts', 'comments'];
$includes = $request->query('include', '');

// Filter to only allowed
$includes = collect(explode(',', $includes))
    ->filter(fn ($i) => in_array($i, $allowedIncludes))
    ->implode(',');
```

## Best Practices

- Use query parameter for client-controlled includes
- Only allow pre-defined relationships (whitelist)
- Use `when()` for conditional loading
- Consider performance impact of includes