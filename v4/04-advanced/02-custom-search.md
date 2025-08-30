# Custom Global Search

## Overview

The Custom Global Search feature provides unlimited flexibility to make absolutely anything in your application searchable. Unlike the standard resource-based search, this feature allows you to implement completely custom search logic that can search static content, external APIs, complex database queries, or any other data source imaginable.

## Key Benefits

- **Universal Search**: Search anything - not just Eloquent models
- **Complete Control**: Define your own search logic, data sources, and result formatting
- **Flexible Integration**: Choose to merge with core search results or replace them entirely
- **Performance Optimized**: Only executes when needed, with proper result limiting
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
}, mergeWithCore: true)
```

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

## Advanced Examples

### Full-Text Search with Elasticsearch

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    $searchResults = Elasticsearch::search([
        'index' => 'documents',
        'body' => [
            'query' => [
                'multi_match' => [
                    'query' => $query,
                    'fields' => ['title^2', 'content', 'tags']
                ]
            ],
            'size' => 20
        ]
    ]);

    $results = collect($searchResults['hits']['hits'])->map(function ($hit) {
        $source = $hit['_source'];
        return new GlobalSearchResult(
            title: $source['title'],
            url: "/admin/documents/{$hit['_id']}",
            details: [
                'Score: ' . round($hit['_score'], 2),
                Str::limit($source['content'], 100)
            ]
        );
    });

    if ($results->count()) {
        $builder->category('Documents', $results);
    }

    return $builder;
})
```

### Multi-Source Aggregated Search

Search multiple data sources and combine results:

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Search local database
    $localUsers = User::where('name', 'like', "%{$query}%")
        ->limit(5)
        ->get()
        ->map(fn($user) => new GlobalSearchResult(
            title: $user->name,
            url: "/admin/users/{$user->id}",
            details: [$user->email, 'Local User']
        ));

    // Search LDAP directory
    $ldapUsers = LDAP::search('users', [
        'filter' => "(cn=*{$query}*)",
        'limit' => 5
    ])->map(fn($user) => new GlobalSearchResult(
        title: $user['cn'][0],
        url: "/admin/ldap-users/{$user['uid'][0]}",
        details: [$user['mail'][0], 'LDAP User']
    ));

    // Search external CRM
    $crmContacts = Http::get("https://api.crm.com/contacts/search", [
        'q' => $query,
        'limit' => 5
    ])->collect()->map(fn($contact) => new GlobalSearchResult(
        title: $contact['name'],
        url: "/admin/crm-contacts/{$contact['id']}",
        details: [$contact['company'], 'CRM Contact']
    ));

    $allUsers = $localUsers->merge($ldapUsers)->merge($crmContacts);

    if ($allUsers->count()) {
        $builder->category('All Users', $allUsers);
    }

    return $builder;
})
```

## Configuration Options
@blade
<x-converge::alert type="info">
    this part all AI docs generated with my refinements and tweacks,it's good exampe to show you the full endless posibilities it gives
</x-converge::alert >
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

### Multiple Custom Search Sources

You can combine custom search with other features:

```php
GlobalSearchModalPlugin::make()
    ->searchCustomPages() // Enable custom pages search
    ->searchUsing(function (string $query, GlobalSearchResults $builder) {
        // Your custom global search logic
        return $builder;
    }, mergeWithCore: true)
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

### Weighted Search Results

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    $results = collect();

    // High priority: Exact matches
    if ($exactMatch = Setting::where('key', $query)->first()) {
        $results->add(new GlobalSearchResult(
            title: "⭐ " . $exactMatch->name,
            url: "/admin/settings?highlight={$exactMatch->key}",
        ));
    }

    // Medium priority: Partial matches
    $partialMatches = Setting::where('name', 'like', "%{$query}%")
        ->get()
        ->map(fn($setting) => new GlobalSearchResult(
            title: $setting->name,
            url: "/admin/settings?highlight={$setting->key}",
        ));

    $results = $results->merge($partialMatches);

    if ($results->count()) {
        $builder->category('Settings', $results);
    }

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

## Real-World Use Cases

### 1. **Application Navigation**
Make any part of your app discoverable through search:
- Admin settings tabs
- Dashboard widgets
- Report sections
- Help documentation

### 2. **External Integrations**
Search external services from within your admin:
- CRM contacts
- Support tickets
- GitHub repositories
- Slack channels

### 3. **System Operations**
Create searchable admin shortcuts:
- Cache clearing
- Database operations
- Log viewing
- System monitoring

### 4. **Content Management**
Search non-model content:
- Configuration files
- Static pages
- Documentation
- Feature flags

### 5. **Analytics & Reporting**
Quick access to reports and analytics:
- Pre-built reports
- Dashboard views
- Data exports
- Performance metrics

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

4. **Cache expensive operations**:
```php
$results = Cache::remember("search_external_{$query}", 300, function () use ($query) {
    return Http::get("https://api.external.com/search?q={$query}");
});
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

## Integration Examples


### Complete Real-World Implementation

```php
GlobalSearchModalPlugin::make()
    ->modal(slideOver: true)
    ->highlightQueryMatches()
    ->searchCustomPages() // Enable custom pages
    ->searchUsing(function (string $query, GlobalSearchResults $builder) {
        
        // 1. Search application settings
        $this->searchSettings($query, $builder);
        
        // 2. Search external CRM
        $this->searchCRM($query, $builder);
        
        // 3. Search documentation
        $this->searchDocs($query, $builder);
        
        // 4. Search system commands
        $this->searchCommands($query, $builder);

        return $builder;
    }, mergeWithCore: true)

private function searchSettings(string $query, GlobalSearchResults $builder): void
{
    $settings = [
        'general' => 'General Configuration',
        'email' => 'Email Settings',
        'security' => 'Security Options',
        'integrations' => 'API Integrations',
    ];

    $results = collect($settings)
        ->filter(fn($title) => str_contains(strtolower($title), strtolower($query)))
        ->map(fn($title, $key) => new GlobalSearchResult(
            title: $title,
            url: "/admin/settings?tab={$key}",
            details: ['System Setting']
        ));

    if ($results->count()) {
        $builder->category('Settings', $results);
    }
}

private function searchCRM(string $query, GlobalSearchResults $builder): void
{
    if (strlen($query) < 3) return; // Skip for short queries

    try {
        $contacts = Http::timeout(3)
            ->get("https://api.crm.com/contacts/search?q={$query}")
            ->collect()
            ->take(5)
            ->map(fn($contact) => new GlobalSearchResult(
                title: $contact['name'],
                url: "/admin/crm/contacts/{$contact['id']}",
                details: [
                    $contact['company'],
                    $contact['email'],
                    'CRM Contact'
                ]
            ));

        if ($contacts->count()) {
            $builder->category('CRM Contacts', $contacts);
        }
    } catch (\Exception $e) {
        // Fail silently or add error result
    }
}
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

    $builder->category('Users', $users);
    $builder->category('Settings', $settings);
    $builder->category('External', $external);
    $builder->category('Commands', $commands);

    return $builder;
}, mergeWithCore: false) // Replace default search entirely
```

## Configuration Patterns

### Environment-Based Search

```php
->searchUsing(function (string $query, GlobalSearchResults $builder) {
    // Production: Only search internal resources
    if (app()->isProduction()) {
        return $this->searchInternalOnly($query, $builder);
    }

    // Development: Include debug tools and external APIs
    $this->searchInternalOnly($query, $builder);
    $this->searchDebugTools($query, $builder);
    $this->searchExternalAPIs($query, $builder);

    return $builder;
})
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

## Best Practices

### 1. Performance Optimization
- Always limit results (`->limit()`, `->take()`)
- Use database indexes for searched columns
- Implement query length minimums for expensive operations
- Cache external API results when possible

### 2. User Experience
- Provide meaningful result details
- Use clear, descriptive category names
- Include relevant actions where appropriate
- Handle empty results gracefully

### 3. Error Handling
- Wrap external calls in try-catch blocks
- Provide fallback results when external services fail
- Log errors for debugging without breaking search

### 4. Security
- Validate and sanitize search queries
- Respect user permissions and roles
- Don't expose sensitive information in search results

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
```

## Conclusion

The Custom Global Search feature transforms your admin panel into a universal search interface. Whether you're searching database records, external APIs, static content, or system commands, this feature provides the flexibility to make everything discoverable through a single, consistent interface.

The key is that you're no longer limited to just Eloquent models - you can search absolutely anything and present it through the familiar global search interface that your users already know and love.