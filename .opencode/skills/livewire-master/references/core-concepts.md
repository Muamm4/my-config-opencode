# Core Concepts

Livewire v3/v4 core patterns including component structure, Volt API, properties, actions, and wire:model modifiers.

## Component Structure

Livewire v3/v4 components are PHP classes that render Blade templates. Properties store state, actions handle user interactions.

```php
<?php

namespace App\Livewire;

use Livewire\Component;

class Counter extends Component
{
    public int $count = 0;

    public function increment(): void
    {
        $this->count++;
    }

    public function render(): View
    {
        return view('livewire.counter');
    }
}
```

```blade
<div>
    <button wire:click="increment">
        Count: {{ $count }}
    </button>
</div>
```

## Volt Functional API

Single-file component approach with closure-based syntax:

```php
use function Livewire\Volt\state;
use function Livewire\Volt\computed;
use function Livewire\Volt\action;
use function Livewire\Volt\layout;

state(['count' => 0]);

$increment = action(fn () => $this->count++);

// Computed property - lazily evaluated and cached within request
$doubledCount = computed(fn () => $this->count * 2);

layout('layouts.app');

return view('livewire.counter');
```

Or with Blade:

```blade
@volt('livewire.counter')
    <div x-data="{ count: @entangle('count') }">
        <button wire:click="increment">
            Count: {{ $count }}
        </button>
    </div>
@endvolt
```

## Properties

**Public Properties** - Automatically synchronized:

```php
public string $title = '';
public int $quantity = 1;
public array $tags = [];
public Collection $posts;
```

**Computed Properties:**

```php
use Livewire\Attributes\Computed;

#[Computed]
public function filteredPosts(): Collection
{
    return Post::where('user_id', auth()->id())
        ->where('title', 'like', "%{$this->search}%")
        ->get();
}

// In Volt:
$filteredPosts = computed(fn () => Post::where(...)->get());
```

**Locked Properties:**

```php
use Livewire\Attributes\Locked;

#[Locked]
public int $id;
```

**Reactive Properties:**

```php
use Livewire\Attributes\Reactive;

#[Reactive]
public array $filters = [];
```

## Actions

```php
public function save(): void
{
    $this->validate();
    Post::create($this->only(['title', 'content']));
    $this->reset();
    session()->flash('success', 'Post created!');
}
```

**Renderless Actions:**

```php
use Livewire\Attributes\Renderless;

#[Renderless]
public function trackAnalytics(): void
{
    // No view update on execution
}
```

## SPA Mode

Enable SPA-like navigation:

```blade
<a href="/posts" wire:navigate>Posts</a>
<a href="/posts/create" wire:navigate.prefetch>Create Post</a>

<!-- Persist elements across navigation -->
<nav @persist><!-- Player persists --></nav>
```

Global SPA mode in AppServiceProvider:

```php
use Livewire\Livewire;
Livewire::configure('spa-mode', true);
```

## wire:model Modifiers

| Modifier | Behavior |
|----------|---------|
| Default (deferred) | Syncs on form submit |
| `.lazy` | Syncs on blur |
| `.live` | Syncs immediately |
| `.debounce.500` | Debounces by 500ms |
| `.defer` | Defers until next request |

**v4 timing control:**

```blade
<!-- immediate sync + debounce -->
<input wire:model.live.debounce.300ms="search">
```

## Attributes Reference

| Attribute | Purpose |
|-----------|---------|
| `#[Computed]` | Dynamic property |
| `#[Locked]` | Read-only property |
| `#[Reactive]` | Parent-reactive |
| `#[Renderless]` | No view update |
| `#[Js]` | Client-side action |
| `#[On('event')]` | Event listener |
| `#[Validate]` | Validation rules |
| `#[Layout('path')]` | Component layout |
| `#[Title('text')]` | Page title |

## Directory Structure

```
app/
├── Livewire/
│   ├── Counter.php
│   ├── Post/
│   │   ├── CreatePost.php
│   │   └── PostList.php
│   └── Concerns/
│       └── HasFilters.php
resources/
└── views/
    └── livewire/
        ├── counter.blade.php
        └── post/
```