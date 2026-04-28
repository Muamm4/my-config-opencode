# JSON:API Response Format

Laravel API Resources following the JSON:API specification for consistent API responses.

## Basic Resource Structure

```php
<?php
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'type' => 'users',
            'attributes' => [
                'name' => $this->name,
                'email' => $this->email,
                'createdAt' => $this->created_at->toIso8601String(),
            ],
        ];
    }
}
```

## Resource with Relationships

```php
<?php
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'type' => 'users',
            'attributes' => [
                'name' => $this->name,
                'email' => $this->email,
                'createdAt' => $this->created_at->toIso8601String(),
            ],
            'relationships' => [
                'posts' => [
                    'data' => PostResource::collection($this->posts),
                ],
            ],
        ];
    }
}
```

## Best Practices

- Use `toIso8601String()` for dates (ISO 8601 standard)
- Always include `type` field for JSON:API compliance
- Group user-facing fields under `attributes`
- Use separate Resource classes for collections vs single resources