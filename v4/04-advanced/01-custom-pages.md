# Making Custom Pages Searchable

## Overview

The plugin searches all resources in your panel by default, but when building custom pages that aren't related to any resource, you might want to make them searchable as well. The plugin provides full flexibility for querying your database and customizing search behavior for custom pages.

## Configuration

Make sure custom pages search is enabled in your plugin configuration (disabled by default for performance and safety reasons):

```php
GlobalSearchModalPlugin::make()
    ->->searchCustomPages() // Enable this feature
```

## Implementation

To add search functionality to your custom page, implement the `CharrafiMed\GlobalSearchModal\Contracts\Searchable` contract:

```php
<?php

namespace App\Filament\Pages;

use CharrafiMed\GlobalSearchModal\Contracts\Searchable;
use Illuminate\Support\Collection;
use Filament\Pages\Page;

class Settings extends Page implements Searchable
{
    protected string $view = 'filament.pages.settings';

    // Required methods to deal with search
    public static function getGlobalSearchResults(string $query): Collection|array
    {
        // ...
    }

    public static function getGlobalSearchGroupName(): string
    {
        // ...
    }
}
```

## Required Methods

- ``getGlobalSearchResults()``

This method defines what results should be returned for the given search query:

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchResult;
public static function getGlobalSearchResults(string $query): Collection|array
{
    // Example: Search posts by title
    return Post::where('title', 'like', "%{$query}%")
        ->limit(10) // Always limit results for performance
        ->get()
        ->map(function (Model $record): GlobalSearchResult {
            return new GlobalSearchResult(
                title: $record->title,
                url: static::generateUrl($record->id),
            );
        });
}
// url generation example 
protected static function generateUrl(string $id): string
{
    return route('settings.show', $id);
}
```

**Parameters:**
- `$query` - The search term entered by the user

**Returns:**
- `Collection|array` - Collection of `GlobalSearchResult` objects


>The part inside `map()` is necessary to build search results in the shape of `GlobalSearchResult`
>every result record must be of type `CharrafiMed\GlobalSearchModal\GlobalSearchResult`  to ensure it aligns with the same data structure used by other resource searches.

- `getGlobalSearchGroupName()`

Defines the group name that appears in search results:

```php
public static function getGlobalSearchGroupName(): string
{
    return 'Posts'; // This will group results under "Posts" category
}
```

**Returns:**
- `string` - The category name for grouping search results

## Authorization

Determines if this page should be included in global search:

```php
public static function canGloballySearch(): bool
{
    return true; // or add your own logic
}
```

**Returns:**
- `bool` - Whether this page is searchable

**Examples:**
```php
// Only allow admins
return auth()->user()?->hasRole('admin');

// Check specific permissions
return auth()->user()?->can('search-posts');

// Check feature flag
return config('features.post_search_enabled', true);
```


## Url generation

Generate URLs for individual search results it's up to you of how you want:

```php
protected static function generateUrl(string $id): string
{
    return route('settings.show', $id);
}
```

- **even raw string**
```php
protected static function generateUrl(string $id): string
{
// Raw string (not recommended for production)
return "http://127.0.0.1:8000/admin/posts/{$id}";
}
```

## Advanced Examples

> you can use any driver for searching, this gives you 100% flexibity for example let's use simple array and add the clicked recored in the query string, you may grab that query string and active a tab or or any crazy thing

### Searching Static Content
```php
use use Illuminate\Support\Collection;

public static function getGlobalSearchResults(string $query): Collection
{
        $sections = [
            'general' => 'General Settings',
            'email' => 'Email Configuration',
            'security' => 'Security Settings',
        ];

        $results = collect();

        foreach ($sections as $key => $title) {
            if (str_contains(strtolower($title), strtolower($query))) {
                $results->add(new GlobalSearchResult(
                    title: $title,
                    url: static::getUrl() . "?section={$key}"
                ));
            }
        }

        return $results;
}
```

## GlobalSearchResult Constructor

The `GlobalSearchResult` class accepts the following parameters:

```php
new GlobalSearchResult(
    title: 'Result Title',           // Required: Main title
    url: 'https://example.com',      // Required: URL to navigate to
    details: ['Detail 1', 'Detail 2'] // Optional: Additional info
    
)
```