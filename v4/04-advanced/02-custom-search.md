# Custom Global Search

## Overview

The Custom Global Search feature provides unlimited flexibility to make absolutely anything in your application searchable. Unlike the standard resource-based search, this feature allows you to implement completely custom search logic that can search static content, external APIs, complex database queries, or any other data source imaginable.

## why we need this ? 

- **Universal Search**: Search anything - not just Eloquent models
- **Complete Control**: Define your own search logic, data sources, and result formatting
- **Flexible Integration**: Choose to merge with core search results or replace them entirely
- **Consistent Interface**: Results integrate seamlessly with existing search UI

## Basic Configuration

Enable custom global search in your panel provider:

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use CharrafiMed\GlobalSearchModal\GlobalSearchResult;
use CharrafiMed\GlobalSearchModal\GlobalSearchResults;

GlobalSearchModalPlugin::make()
    ->searchUsing(function (string $query, GlobalSearchResults $builder) {
        // Your custom search logic here
        return $builder;
    })
```

## Method Signature

```php
->searchUsing(
    Closure $callback,
    bool $mergeWithCore = true
)
```

**Parameters:**
- `$callback`: Function that receives `($query, $builder)` and returns `GlobalSearchResults`
- `$mergeWithCore`: Whether to merge with standard resource search or replace it entirely

**Callback Parameters:**
- `string $query`: The search term entered by the user
- `GlobalSearchResults $builder`: Builder instance for adding categories and results

## Basic Examples

### Searching Static Content

Perfect for application settings, navigation items, or configuration options:

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    $sections = [
        'general' => 'General Settings',
        'email' => 'Email Configuration',
        'security' => 'Security Settings',
        'billing' => 'Billing & Payments',
        'integrations' => 'Third-party Integrations',
    ];

    $results = collect();

    foreach ($sections as $key => $title) {
        if (str_contains(strtolower($title), strtolower($query))) {
            $results->add(new GlobalSearchResult(
                title: $title,
                url: "/admin/settings?tab={$key}",
            ));
        }
    }

    if ($results->count()) {
        $builder->category('Settings', $results);
    }

    return $builder;
})
```

### Searching External APIs

Search external services and integrate results into your admin panel:

```php
use Illuminate\Support\Facades\Http;

->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Search GitHub repositories
    $githubResults = Http::get("https://api.github.com/search/repositories", [
        'q' => $query,
        'per_page' => 5
    ])->json('items', []);

    $results = collect($githubResults)->map(function ($repo) {
        return new GlobalSearchResult(
            title: $repo['name'],
            url: $repo['html_url'],
            details: [
                $repo['language'] ?? 'Unknown',
                $repo['stargazers_count'] . ' stars'
            ]
        );
    });

    if ($results->count()) {
        $builder->category('GitHub Repositories', $results);
    }

    return $builder;
}, mergeWithCore: false) // Modal results will show only this function's builder results
```

Now type a query like "global search modal" and you will see the plugin repository shown to you.

### Complex Database Queries

Search across multiple tables with custom logic:

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Search across multiple related models
    $orders = Order::whereHas('customer', function ($q) use ($query) {
        $q->where('name', 'like', "%{$query}%");
    })
    ->orWhere('order_number', 'like', "%{$query}%")
    ->with('customer')
    ->limit(10)
    ->get();

    $results = $orders->map(function ($order) {
        return new GlobalSearchResult(
            title: "Order #{$order->order_number}",
            url: "/admin/orders/{$order->id}",
            details: [
                $order->customer->name,
                $order->created_at->format('M d, Y'),
                '$' . number_format($order->total, 2)
            ]
        );
    });

    if ($results->count()) {
        $builder->category('Orders', $results);
    }

    return $builder;
})
```

@blade
<x-converge::alert type="info">
    As long as the builder is an instance of `CharrafiMed\GlobalSearchModal\GlobalSearchResults` and results are a collection of `CharrafiMed\GlobalSearchModal\GlobalSearchResult`, the plugin works fine regardless of data source.
</x-converge::alert>
@endblade

### Merge with Core Search

**Merge Mode (default)**: Combines your custom results with standard resource search
```php
->searchUsing($callback, mergeWithCore: true)
```

**Replace Mode**: Your custom search completely replaces the default search
```php
->searchUsing($callback, mergeWithCore: false)
```

## Advanced Examples

@blade
<x-converge::alert type="info">
    This part is all AI-generated docs with my refinements and tweaks. It's a good example to show you the full endless possibilities it provides.
</x-converge::alert>
@endblade

### Using Laravel Scout

Laravel Scout provides a driver-based solution for full-text search. Here's how to integrate it with Custom Global Search:

```php
use App\Models\Post;
use App\Models\Product;

->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Search posts using Scout
    $posts = Post::search($query)
        ->take(10)
        ->get()
        ->map(function ($post) {
            return new GlobalSearchResult(
                title: $post->title,
                url: "/admin/posts/{$post->id}",
                details: [
                    $post->category->name ?? 'Uncategorized',
                    $post->published_at?->format('M d, Y') ?? 'Draft',
                    $post->author->name
                ]
            );
        });

    // Search products using Scout with custom scoring
    $products = Product::search($query)
        ->query(fn($builder) => $builder->where('active', true))
        ->take(8)
        ->get()
        ->map(function ($product) {
            return new GlobalSearchResult(
                title: $product->name,
                url: "/admin/products/{$product->id}",
                details: [
                    $product->category,
                    '$' . number_format($product->price, 2),
                    $product->stock > 0 ? 'In Stock' : 'Out of Stock'
                ]
            );
        });

    // Add categories to builder
    if ($posts->count()) {
        $builder->category('Blog Posts', $posts);
    }

    if ($products->count()) {
        $builder->category('Products', $products);
    }

    return $builder;
})
```
## Advanced Patterns

### Conditional Search Sources

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Only search external APIs for longer queries
    if (strlen($query) >= 3) {
        // External API search logic
    }

    // Always search local static content
    // Static content search logic

    return $builder;
})
```

### Search with Actions

```php
use Filament\Actions\Action;

->searchUsing(function (string $query, GlobalSearchResults $builder) {
    $results = collect([
        [
            'title' => 'Clear Application Cache',
            'action' => 'cache:clear',
        ],
    ])->filter(function ($item) use ($query) {
        return str_contains(strtolower($item['title']), strtolower($query));
    })->map(function ($item) {
        return new GlobalSearchResult(
            title: $item['title'],
            url: '#',
            details: [
                'This is a system-level command - be sure before executing'
            ],
            actions: [
                Action::make('run')
                    ->label('Execute')
                    ->action(fn() => Artisan::call($item['action']))
                    ->requiresConfirmation()
            ]
        );
    });

    if ($results->count()) {
        $builder->category('System Commands', $results);
    }

    return $builder;
})
```

## Performance Considerations

### Best Practices

1. **Always limit results**:
```php
->limit(10) // For database queries
// or
->take(10) // For collections
```

2. **Use efficient queries**:
```php
// Good: Indexed column search
User::where('email', 'like', "%{$query}%")

// Better: Full-text search if available
User::whereFullText(['name', 'email'], $query)
```

3. **Implement minimum query length**:
```php
if (strlen($query) < 2) {
    return $builder; // Skip expensive operations for short queries
}
```

## Error Handling

### Graceful Degradation

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    try {
        // Potentially failing external API call
        $results = Http::timeout(5)->get("https://api.external.com/search", [
            'q' => $query
        ])->json();
        
        // Process results...
        
    } catch (\Exception $e) {
        // Log error but don't break search
        Log::warning('External search failed', ['query' => $query, 'error' => $e->getMessage()]);
        
        // Optionally add a fallback result
        $builder->category('External Search', collect([
            new GlobalSearchResult(
                title: 'External search temporarily unavailable',
                url: '#',
            )
        ]));
    }

    return $builder;
})
```

## Migration from Standard Search

### Before (Limited to Resources)
```php
// Only User resource results available
class UserResource extends Resource
{
    public static function getGlobalSearchResultTitle(Model $record): string
    {
        return $record->name;
    }
}
```

### After (Unlimited Possibilities)
```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Search users (same as before)
    $users = User::where('name', 'like', "%{$query}%")
        ->limit(5)
        ->get()
        ->map(fn($user) => new GlobalSearchResult(
            title: $user->name,
            url: "/admin/users/{$user->id}",
        ));

    // Plus search anything else
    $settings = $this->searchSettings($query);
    $external = $this->searchExternalAPI($query);
    $commands = $this->searchCommands($query);

    if($users->count()){
        $builder->category('Users', $users);
    }
    // ...
    $builder->category('Settings', $settings);
    //...
    $builder->category('External', $external);
    //...
    $builder->category('Commands', $commands);

    return $builder;
}, mergeWithCore: false) // Replace default search entirely
```

### Role-Based Search Results

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    $user = auth()->user();

    // Everyone can search basic content
    $this->searchBasicContent($query, $builder);

    // Admins get additional search capabilities
    if ($user->hasRole('admin')) {
        $this->searchSystemCommands($query, $builder);
        $this->searchAuditLogs($query, $builder);
    }

    // Developers get debug tools
    if ($user->hasRole('developer')) {
        $this->searchDebugTools($query, $builder);
        $this->searchLogs($query, $builder);
    }

    return $builder;
})
```

## Common Patterns

### Search Strategy Pattern

```php
interface SearchStrategy 
{
    public function search(string $query): Collection;
    public function getCategoryName(): string;
}

class SettingsSearchStrategy implements SearchStrategy
{
    public function search(string $query): Collection
    {
        // Settings search logic
    }
    
    public function getCategoryName(): string
    {
        return 'Settings';
    }
}

// Usage in searchUsing
$strategies = [
    new SettingsSearchStrategy(),
    new ExternalAPISearchStrategy(),
    new DocumentSearchStrategy(),
];

foreach ($strategies as $strategy) {
    $results = $strategy->search($query);
    if ($results->count()) {
        $builder->category($strategy->getCategoryName(), $results);
    }
}