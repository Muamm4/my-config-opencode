# Livewire Master Skill

## Overview

This skill provides comprehensive technical guidance for building production-ready Laravel Livewire v3/v4 applications. It covers core concepts, performance optimization, state management, Alpine.js integration, and common pitfalls with detailed patterns and code snippets.

---

## 1. Core Concepts

### 1.1 Component Structure

Livewire v3/v4 components are PHP classes that render Blade templates. Properties store state, actions handle user interactions, and the framework handles all JavaScript reactivity.

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

### 1.2 Volt Functional API

Volt provides a single-file component approach where PHP logic and Blade templates coexist:

```blade
@volt('livewire.counter')
    <div x-data="{ count: @entangle('count') }">
        <button wire:click="increment">
            Count: {{ $count }}
        </button>
    </div>
@endvolt
```

With the Volt functional API (closure-based):

```php
use function Livewire\Volt\state;
use function Livewire\Volt\computed;
use function Livewire\Volt\action;
use function Livewire\Volt\layout;

state(['count' => 0]);

$increment = action(fn () => $this->count++);

$decrement = action(fn () => $this->count--);

// Computed property - lazily evaluated and cached within request
$doubledCount = computed(fn () => $this->count * 2);

// Layout for Volt components
layout('layouts.app');

return view('livewire.counter');
```

**Class-based Volt** (traditional syntax in single file):

```php
<?php

use Livewire\Volt\Component;

new class extends Component {
    public $count = 0;

    public function increment(): void
    {
        $this->count++;
    }

    public function render(): View
    {
        return view('livewire.counter');
    }
} ?>
```

### 1.3 Properties

**Public Properties** - Automatically synchronized between backend and frontend:

```php
public string $title = '';
public int $quantity = 1;
public array $tags = [];
public Collection $posts;
```

**Computed Properties** - Evaluated on-the-fly with the `#[Computed]` attribute or `computed()` function:

```php
use Livewire\Attributes\Computed;

#[Computed]
public function filteredPosts(): Collection
{
    return Post::where('user_id', auth()->id())
        ->where('title', 'like', "%{$this->search}%")
        ->get();
}

// Alternative in Volt:
$filteredPosts = computed(fn () => Post::where('user_id', auth()->id())->get());
```

**Locked Properties** - Cannot be modified from the frontend:

```php
use Livewire\Attributes\Locked;

#[Locked]
public int $id;

#[Locked]
public string $uuid;
```

**Reactive Properties** - Automatically update child components when parent changes:

```php
use Livewire\Attributes\Reactive;

#[Reactive]
public array $filters = [];
```

### 1.4 Actions

Actions are methods triggered by user interactions:

```php
public function save(): void
{
    $this->validate();
    
    Post::create($this->only(['title', 'content']));
    
    $this->reset();
    
    session()->flash('success', 'Post created!');
}

public function delete(Post $post): void
{
    $this->authorize('delete', $post);
    
    $post->delete();
    
    $this->dispatch('post-deleted', id: $post->id);
}

// Actions can accept parameters from the Blade template
public function updateQuantity(int $productId, int $delta): void
{
    $product = Product::find($productId);
    $product->increment('quantity', $delta);
    $product->save();
}
```

**Renderless Actions** - Execute logic without re-rendering:

```php
use Livewire\Attributes\Renderless;

#[Renderless]
public function trackAnalytics(): void
{
    // Analytics tracking code - no view update
}
```

### 1.5 SPA Mode (Single Page Application)

Enable SPA-like navigation using `wire:navigate`:

```blade
<a href="/posts" wire:navigate>Posts</a>
<a href="/posts/create" wire:navigate>Create Post</a>
```

This fetches pages via AJAX and swaps content without full reloads. Combine with `@persist` for elements that should survive navigation:

```blade
<nav>
    <div @persist>
        <!-- Player persists across page navigation -->
    </div>
</nav>
```

**Navigation modifiers:**

```blade
<a href="/posts" wire:navigate.hover>Preload on hover</a>
<a href="/posts" wire:navigate.prefetch>Prefetch on mount</a>
```

**Global SPA Mode** (in AppServiceProvider or bootstrap):

```php
use Livewire\Livewire;

Livewire::configure('spa-mode', true);
```

### 1.6 wire:model Modifiers

Control when data synchronizes with the server:

| Modifier | Behavior |
|----------|---------|
| Default (deferred) | Syncs on form submit |
| `.lazy` | Syncs on blur (tab away) |
| `.live` | Syncs on every change |
| `.debounce.500` | Debounces by 500ms |
| `.defer` | Defers until next request |

```blade
<!-- Default: deferred -->
<input wire:model="search" placeholder="Search...">

<!-- Live with debounce -->
<input wire:model.live.debounce.300ms="search" placeholder="Search...">

<!-- Lazy -->
<input wire:model.lazy="email" placeholder="Email">

<!-- Blur + Enter -->
<input wire:model.blur.enter="search">
```

**v4 timing control** - Use `.live` before modifier to retain immediate client-side sync:

```blade
<!-- v4: immediate sync + debounce -->
<input wire:model.live.debounce.300ms="search">
```

---

## 2. Performance Optimization

### 2.1 Lazy Loading Components

Load components only when visible in viewport:

```blade
<!-- Lazy loads when scrolled into view -->
@livewire('heavy-chart', lazy: true)

<!-- Alternative syntax -->
<livewire:heavy-chart lazy />

<!-- Deferred loads after initial page load -->
@livewire('dashboard-stats', defer: true)
<livewire:dashboard-stats defer />

<!-- Bundle multiple lazy components -->
<livewire:revenue-widget lazy.bundle />
<livewire:orders-widget lazy.bundle />
<livewire:users-widget lazy.bundle />
```

**Using the #[Lazy] attribute** on component class:

```php
use Livewire\Attributes\Lazy;

#[Lazy]
class HeavyChart extends Component
{
    // ...
}
```

**With bundle option** for combined network requests:

```php
#[Lazy(bundle: true)]
class Revenue extends Component
{
    // Multiple components bundle into single request
}
```

**With Placeholder:**

```blade
@livewire('heavy-chart', lazy: true, placeholder: true)
```

```php
public function placeholder(): View
{
    return view('livewire.placeholders.skeleton');
}
```

### 2.2 Bundling and Isolation

**Bundling** - Multiple components update in one request:

```php
// Components bundle by default
// Parent update triggers child updates together
```

**Isolation** - Run components in parallel (don't wait for each other):

```php
use Livewire\Attributes\Isolate;

#[Isolate]
class ExpensiveWidget extends Component
{
    // Runs independently, doesn't block other updates
}
```

### 2.3 Eager Loading

Prevent N+1 queries:

```php
#[Computed]
public function postsWithRelations(): Collection
{
    return Post::with(['user', 'comments', 'tags'])
        ->latest()
        ->get();
}
```

### 2.4 Pagination

```php
use Livewire\WithPagination;

class PostList extends Component
{
    use WithPagination;

    public function render(): View
    {
        return view('livewire.post-list', [
            'posts' => Post::latest()->paginate(20)
        ]);
    }
}
```

```blade
<div>
    @foreach($posts as $post)
        {{ $post->title }}
    @endforeach
    
    {{ $posts->links() }}
</div>
```

### 2.5 Debouncing Actions

```blade
<!-- Debounce the search action -->
<input wire:model="search" wire:debounce.300ms="search">

<!-- Poll with custom interval -->
<div wire:poll.1000s="refresh">
    <!-- Updates every 60 seconds -->
</div>
```

### 2.6 wire:ignore for Static Content

Exclude elements from Livewire's morphing:

```blade
<footer wire:ignore>
   Static footer - not processed by Livewire
</footer>
```

### 2.7 Query Optimization

```php
// Select only needed columns
$posts = Post::select(['id', 'title', 'slug'])
    ->with(['user:id,name'])
    ->get();

// Use exists() for boolean checks
$hasPosts = Post::where('user_id', $this->userId)
    ->exists();
```

### 2.8 Defer Attribute

Load component immediately after initial page load but not on initial render:

```php
use Livewire\Attributes\Defer;

#[Defer]
class DashboardStats extends Component
{
    // Loads in background after page renders
}
```

---

## 3. Advanced State Management

### 3.1 Parent-Child Communication

**Passing Props:**

```blade
@livewire('child-component', ['message' => 'Hello from parent'])
// or
<livewire:child-component message="Hello from parent" />
```

```php
// In ChildComponent:
public function mount(): void
{
    $this->message = $this->message ?? 'Default';
}

public $message;
```

**Reactive Props:**

```php
// Parent passes reactive data
@livewire('todo-list', ['todos' => $todos])

// Child uses #[Reactive]
use Livewire\Attributes\Reactive;

#[Reactive]
public array $todos = [];
```

**Direct Parent Access:**

```blade
<!-- In child template, access parent methods directly -->
<button wire:click="$parent.remove({{ $todo->id }})">Remove</button>
```

### 3.2 Event System

**Dispatching Events:**

```php
// From any component method
$this->dispatch('post-created', postId: $post->id);

// Dispatch to specific component
$this->dispatch('notify', message: 'Saved!')
    ->to(ShoppingCart::class);

// Dispatch to self only
$this->dispatch('refresh')
    ->self();

// Dispatch upward to parent
$this->dispatch('child-updated')
    ->up();
```

**Listening for Events with #[On]:**

```php
use Livewire\Attributes\On;

#[On('post-created')]
public function handlePostCreated(int $postId): void
{
    $this->posts = Post::latest()->get();
}

// Also handles events from JavaScript
#[On('post-created')]
public function onPostCreated(int $postId): void
{
    // Handle event from JavaScript
}
```

**Calling from JavaScript:**

```javascript
// From any JS
Livewire.dispatch('post-created', { postId: 5 });

// From Alpine
<button @click="$dispatch('increment')">+</button>

// With data
<button @click="$dispatch('post-created', { title: 'Post Title' })">Create</button>
```

### 3.3 Sibling Component Communication

```php
// Component A dispatches event
$this->dispatch('item-added', item: $this->item);

// Component B listens
#[On('item-added')]
public function onItemAdded(array $item): void
{
    $this->items[] = $item;
}
```

### 3.4 Two-Way Binding Between Components (Modelable)

Enable child components to bind via `wire:model`:

```blade
<!-- Parent -->
<livewire:search-filter bind:selectedCategories />
<!-- or -->
<livewire:search-filter wire:model="selectedCategories" />
```

```php
// In SearchFilter component:
use Livewire\Attributes\Modelable;

#[Modelable]
public $selectedCategories = [];

// Template must use wire:model
<select wire:model="selectedCategories" multiple>
    <option value="php">PHP</option>
    <option value="laravel">Laravel</option>
</select>
```

**Alternative syntax in Volt:**

```php
state(['form'])->modelable();
```

### 3.5 Global State with URL Query Strings

```php
// Bind properties to URL
protected $queryString = [
    'search' => ['except' => ''],
    'page' => ['except' => 1],
    'filters' => ['except' => []],
];
```

```blade
<a href="?search=laravel">Filter</a>
<a href="?page={{ $page + 1 }}">Next</a>
```

### 3.6 Session-Based Persistence

```php
public function persist(): array
{
    return [
        'cart' => $this->cart,
    ];
}

// Restore in mount()
public function mount(): void
{
    $this->cart = session()->get('cart', []);
}
```

### 3.7 Component Lifecycle Hooks

```php
public function mount(): void
{
    // Called once when component is mounted
    $this->title = 'Default Title';
}

public function boot(): void
{
    // Called on every request, before validation
}

public function updating(string $property, mixed $value): void
{
    // Called BEFORE any property is updated
    if ($property === 'content' && strlen($value) > 10000) {
        throw new \Exception('Content too long');
    }
}

public function updated(string $property, mixed $value): void
{
    // Called AFTER any property is updated
    // Auto-save on change
    $this->post->update([$property => $value]);
}
```

### 3.8 Layout Integration

Set component-specific layout with `#[Layout]` attribute:

```php
use Livewire\Attributes\Layout;
use Livewire\Attributes\Title;

#[Layout('layouts.dashboard')]
#[Title('Dashboard')]
class Dashboard extends Component
{
    // ...
}
```

**In Volt:**

```php
use function Livewire\Volt\layout;

layout('layouts.app');

return view('livewire.dashboard');
```

---

## 4. Alpine.js Integration

### 4.1 The $wire Object (JavaScript Bridge)

Access Livewire properties from JavaScript:

```blade
<!-- Read property -->
<span x-text="$wire.count"></span>

<!-- Mutate property -->
<button x-on:click="$wire.count++">+</button>

<!-- Call method -->
<button x-on:click="$wire.save()">Save</button>
```

**In JavaScript files:**

```javascript
let component = Livewire.first();
console.log(component.count);
component.increment();
```

### 4.2 Entangle for Two-Way Sync

Creates a reactive link between Alpine and Livewire:

```blade
<div x-data="{ open: $wire.entangle('isOpen') }">
    <button @click="open = !open">Toggle</button>
    <div x-show="open">Content</div>
</div>
```

**Entangle with default value:**

```blade
<!-- Local state initialized from Livewire -->
<div x-data="{ title: $wire.entangle('title').defer }">
    <input x-model="title">
</div>
```

### 4.3 Magic Actions

```blade
<!-- Refresh component -->
<button x-on:click="$wire.$refresh()">Refresh</button>

<!-- Set property -->
<button x-on:click="$wire.$set('count', 0)">Reset</button>

<!-- Dispatch event -->
<button x-on:click="$wire.$dispatch('custom-event')">Dispatch</button>
```

### 4.4 Combining Directives

```blade
<!-- Modal with transitions -->
<button x-on:click="$wire.isOpen = true">Open</button>

<div x-show="$wire.isOpen"
     x-transition
     wire:click="$wire.isOpen = false"
     class="fixed inset-0 bg-black/50">
    <div class="bg-white p-6 rounded" @click.stop>
        Content
    </div>
</div>
```

### 4.5 Using Alpine Utilities with Livewire

```blade
<!-- x-show with Livewire property -->
<div x-show="$wire.isOpen">Modal</div>

<!-- x-transition -->
<div x-show="$wire.isOpen"
     x-transition:enter="transition ease-out duration-200"
     x-transition:enter-start="opacity-0"
     x-transition:enter-end="opacity-100">
</div>

<!-- x-model with Livewire -->
<input x-model="$wire.title">

<!-- x-on:blur -->
<input wire:model="email" x-on:blur="$wire.validateEmail()">

<!-- x-intersect for lazy loading on scroll -->
<div x-intersect.once="$wire.loadMore()">Load more</div>
```

### 4.6 JavaScript Actions (Client-Side Only)

Execute JavaScript directly from Livewire without network request:

```php
use Livewire\Attributes\Js;

#[Js]
public function thisIsJs(): string
{
    return <<<'JS'
        (el, component) => {
            el.classList.toggle('active');
            // component is the Livewire instance
            console.log(component.count);
        }
    JS;
}
```

```blade
<button wire:js="thisIsJs">Toggle CSS</button>
```

### 4.7 Passing Alpine Data to Livewire Actions

```php
public function addTodo(string $todo): void
{
    $this->todos[] = $todo;
}
```

```blade
<div x-data="{ todo: '' }">
    <input type="text" x-model="todo">
    <button x-on:click="$wire.addTodo(todo)">Add Todo</button>
</div>
```

### 4.8 Dispatching Livewire Events from Alpine

```blade
<button x-on:click="$dispatch('post-created')">...</button>

<button x-on:click="$dispatch('post-created', { title: 'Post Title' })">...</button>
```

### 4.9 Window Events

```blade
<!-- Close modal on Escape -->
<div x-show="$wire.isOpen" @keydown.escape.window="$wire.isOpen = false">
    Modal Content
</div>

<!-- Handle browser back button -->
@push('scripts')
<script>
    window.addEventListener('popstate', () => {
        Livewire.navigate('/');
    });
</script>
@endpush
```

### 4.10 Important: Remove Duplicate Alpine CDN

Livewire v3/v4 includes Alpine.js internally. Remove duplicate CDN:

```blade
<!-- Remove this: -->
<script src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>

<!-- Use Livewire's included scripts -->
@livewireScripts
```

---

## 5. Common Pitfalls and Best Practices

### 5.1 wire:key in Loops

**Always include unique keys:**

```blade
@foreach($posts as $post)
    <div wire:key="post-{{ $post->id }}">
        {{ $post->title }}
    </div>
@endforeach
```

### 5.2 Property Hydration (Security)

Properties are "dehydrated" before sending to frontend. **Never expose sensitive data**:

```php
// BAD - exposes entire user model with passwords, tokens
public User $user;

// GOOD - only expose safe fields
public string $userName = '';
public string $userEmail = '';

// For collections, map to safe DTOs
public array $userList = [];

#[Computed]
public function userList(): array
{
    return User::select('id', 'name', 'email')->get()
        ->toArray();
}
```

### 5.3 Validation

```php
public function save(): void
{
    $this->validate([
        'title' => 'required|min:3|max:255',
        'email' => 'required|email',
    ]);
    
    // Or use Form Request
    $this->validate($this->rules());
}

protected function rules(): array
{
    return [
        'title' => ['required', 'min:3', 'max:255'],
        'email' => ['required', 'email'],
    ];
}
```

Display validation errors:

```blade
<input wire:model="title">
@error('title')
    <span class="error">{{ $message }}</span>
@enderror
```

### 5.4 Lifecycle Hooks

```php
public function mount(): void
{
    // Called once when component is mounted
}

public function boot(): void
{
    // Called on every request, before validation
}

public function hydrate(): void
{
    // Called on subsequent requests (after first)
}

public function updating(string $key, mixed $value): void
{
    // Called BEFORE any property is updated
}

public function updated(string $key, mixed $value): void
{
    // Called AFTER any property is updated
}
```

### 5.5 Security Best Practices

```php
// Always authorize actions
public function delete(Post $post): void
{
    $this->authorize('delete', $post);
    $post->delete();
}

// Use $this->only() to whitelist fields
$data = $this->only(['title', 'content', 'status']);

// Use #[Locked] for IDs that shouldn't change
use Livewire\Attributes\Locked;

#[Locked]
public int $postId;
```

### 5.6 Error Handling

```php
public function save(): void
{
    try {
        DB::beginTransaction();
        
        // Perform operation
        
        DB::commit();
        
        session()->flash('success', 'Saved!');
    } catch (\Exception $e) {
        DB::rollBack();
        
        $this->dispatch('notify', [
            'type' => 'error',
            'message' => $e->getMessage()
        ]);
    }
}
```

### 5.7 N+1 Query Prevention

```php
#[Computed]
public function postsWithRelations(): Collection
{
    return Post::with(['user', 'tags', 'comments.user'])
        ->get();
}
```

### 5.8 Debug Mode

Enable in development:

```blade
@livewire('component', debug: true)
```

Or in config/livewire.php:

```php
'app' => [
    'debug' => [
        'strict' => true,
    ],
],
```

### 5.9 Duplicate Alpine CDN

Livewire v3/v4 includes Alpine.js internally. **Remove duplicate CDN:**

```blade
<!-- Delete this: -->
<script src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>

<!-- Use Livewire's included scripts -->
@livewireScripts
```

### 5.10 Snapshot Errors

Common causes:
- Missing `wire:key` in loops
- Conditional rendering without keys
- Dynamic components with changing keys

Fix:

```blade
@foreach($posts as $post)
    <livewire:post-card :key="$post->id" :post="$post" />
@endforeach
```

### 5.11 Browser Console Debugging

Inspect Livewire components directly from browser console:

```javascript
// Get first component on page
let $wire = Livewire.first()

// Inspect component state
console.log($wire.count)

// Call methods
$wire.increment()

// Get full component instance
let component = Livewire.first().__instance()
console.log(component.snapshot)
```

**Request Interceptors:**

```javascript
// Intercept and log requests
Livewire.interceptRequest(({ onSend }) => {
    onSend(() => {
        console.log('Request sent:', Date.now());
    });
});

// Handle responses
Livewire.interceptRequest(({ onSuccess, onError }) => {
    onSuccess(({ response, responseJson }) => {
        console.log('Success:', responseJson);
    });
    
    onError(({ response, responseBody, preventDefault }) => {
        console.error('Error:', responseBody);
    });
});
```

### 5.12 Troubleshooting Guide

| Issue | Solution |
|-------|---------|
| Component not loading | Check `@livewireScripts` in layout |
| Missing data in child | Use `#[Reactive]` on prop |
| Events not firing | Verify `#[On]` attribute on listener |
| Slow performance | Use `#[Computed]`, lazy loading |
| Validation not showing | Use `@error` directive |
| Alpine not working | Remove duplicate Alpine CDN |

---

## 6. Testing

### 6.1 Unit Testing Components

```php
use App\Livewire\Counter;
use Livewire\Livewire;

it('increments count', function () {
    Livewire::test(Counter::class)
        ->assertSee('Count: 0')
        ->call('increment')
        ->assertSee('Count: 1');
});

it('resets count', function () {
    Livewire::test(Counter::class)
        ->set('count', 5)
        ->call('reset')
        ->assertSee('Count: 0');
});
```

### 6.2 Integration Testing

```php
it('creates post', function () {
    Livewire::test(CreatePost::class)
        ->set('title', 'Test Post')
        ->set('content', 'Test content')
        ->call('save')
        ->assertRedirect();
    
    $this->assertDatabaseHas('posts', [
        'title' => 'Test Post'
    ]);
});
```

### 6.3 Event Testing

```php
it('dispatches event on delete', function () {
    Livewire::test(PostList::class)
        ->call('delete', $post->id)
        ->dispatched('post-deleted', fn ($event) => 
            $event['postId'] === $post->id
        );
});
```

### 6.4 Validation Testing

Test field-level validation errors:

```php
it('title field is required', function () {
    Livewire::test(PostCreate::class)
        ->set('title', '')
        ->call('save')
        ->assertHasErrors('title');
});

it('title must be at least 3 characters', function () {
    Livewire::test(PostCreate::class)
        ->set('title', 'ab')
        ->call('save')
        ->assertHasErrors(['title' => ['min:3']]);
});

it('email must be valid format', function () {
    Livewire::test(UserCreate::class)
        ->set('email', 'invalid-email')
        ->call('save')
        ->assertHasErrors(['email' => 'email']);
});
```

Test multiple field errors:

```php
it('validates multiple fields', function () {
    Livewire::test(PostCreate::class)
        ->set('title', '')
        ->set('content', '')
        ->call('save')
        ->assertHasErrors(['title', 'content']);
});
```

### 6.5 Property Testing

```php
it('sets initial property value', function () {
    Livewire::test(EditPost::class, ['post' => $post])
        ->assertSet('title', $post->title);
});

it('updates property', function () {
    Livewire::test(Counter::class)
        ->set('count', 10)
        ->call('increment')
        ->assertSet('count', 11);
});
```

### 6.6 View Testing

```php
it('shows loading state', function () {
    Livewire::test(FormWithLoading::class)
        ->call('submit')
        ->assertSet('loading', true)
        ->assertSee('Saving...')
        ->assertStatus(200); // After request completes
});
```

### 6.7 Event Dispatching in Tests

```php
it('listens for emitted events', function () {
    Livewire::test(ParentComponent::class)
        ->dispatch('child-event', ['data' => 'test'])
        ->assertSee('Received: test');
});
```

### 6.8 Lifecycle Testing

```php
it('mount runs on initialization', function () {
    Livewire::test(ComponentWithMount::class)
        ->assertSet('initialized', true);
});

it('updated hook runs after property change', function () {
    Livewire::test(ComponentWithUpdated::class)
        ->set('count', 5)
        ->assertSet('updated', true);
});
```

### 6.2 Integration Testing

```php
it('creates post', function () {
    Livewire::test(CreatePost::class)
        ->set('title', 'Test Post')
        ->set('content', 'Test content')
        ->call('save')
        ->assertRedirect();
    
    $this->assertDatabaseHas('posts', [
        'title' => 'Test Post'
    ]);
});
```

### 6.3 Event Testing

```php
it('dispatches event on delete', function () {
    Livewire::test(PostList::class)
        ->call('delete', $post->id)
        ->dispatched('post-deleted', fn ($event) => 
            $event['postId'] === $post->id
        );
});
```

---

## 7. Directory Structure

Recommended organization:

```
app/
├── Livewire/
│   ├── Counter.php
│   ├── Post/
│   │   ├── CreatePost.php
│   │   ├── EditPost.php
│   │   └── PostList.php
│   └── Concerns/
│       └── HasFilters.php
resources/
└── views/
    └── livewire/
        ├── counter.blade.php
        └── post/
            ├── create-post.blade.php
            └── post-list.blade.php
```

---

## 8. Quick Reference

### Key Directives

| Directive | Purpose |
|-----------|---------|
| `wire:click` | Handle click events |
| `wire:submit` | Handle form submission |
| `wire:model` | Two-way binding |
| `wire:model.lazy` | Sync on blur |
| `wire:model.live` | Sync immediately |
| `wire:change` | Handle select/checkbox changes |
| `wire:poll` | Auto-refresh |
| `wire:navigate` | SPA navigation |
| `wire:loading` | Show loading state |
| `wire:target` | Target specific element |
| `wire:confirm` | Confirmation dialog |

### Magic Properties

| Property | Description |
|----------|-----------|
| `$wire.property` | Get/set property |
| `$wire.action()` | Call action |
| `$wire.$refresh()` | Refresh component |
| `$wire.$set()` | Set property value |
| `$wire.$dispatch()` | Dispatch event |
| `$wire.entangle()` | Sync with Alpine |

### Attributes

| Attribute | Purpose |
|-----------|---------|
| `#[Computed]` | Dynamic property |
| `#[Locked]` | Read-only property |
| `#[Reactive]` | Parent-reactive |
| `#[Isolate]` | Independent update |
| `#[Lazy]` | Lazy load |
| `#[On('event')]` | Event listener |
| `#[Validate]` | Validation rules |
| `#[Renderless]` | No view update |
| `#[Js]` | Client-side action |