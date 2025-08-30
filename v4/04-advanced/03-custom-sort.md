---
title: "Custom Sort for Categories"
---

## Overview

The Custom Sort feature allows you to control the order in which search result categories appear in your global search modal. This ensures that the most important or frequently accessed content appears first, improving user experience and workflow efficiency.

## Basic Usage

Configure category sorting in your panel provider:

```php
use App\Filament\Resources\PostResource;
use App\Filament\Resources\UserResource;

GlobalSearchModalPlugin::make()
    ->sortUsing([
        PostResource::class,  // Will appear as "Posts" 
        UserResource::class,  // Will appear as "Users"
        'Settings',           // Custom category name
        'Commands'            // Custom category name
    ])
```

## Method Signature

```php
->sortUsing(array|Closure|null $items = [])
```

**Parameters:**
- `array`: Direct array of sort items
- `Closure`: Function that returns array of sort items (for dynamic sorting)
- `null`: No custom sorting applied

**Sort Items Can Be:**
- **Resource Classes**: `PostResource::class` (automatically converted to plural model label)
- **Category Names**: `'Settings'`, `'Commands'` (exact string match with category names)

## Dynamic Sorting

Use a closure for conditional or user-based sorting:

```php
->sortUsing(function () {
    $user = auth()->user();
    
    $baseSort = [PostResource::class];
    
    if ($user->hasRole('admin')) {
        $baseSort[] = 'System Commands';
        $baseSort[] = 'Audit Logs';
    }
    
    $baseSort[] = UserResource::class;
    $baseSort[] = 'Settings';
    
    return $baseSort;
})
```

@blade
<x-converge::alert>
    As long as the sort category name is correct, this feature works with standard search, custom pages that are searchable, and any search results coming from the `searchUsing` function (like searching GitHub API, external services, etc.).
</x-converge::alert>
@endblade

## Performance Notes

- Sorting happens after all search results are collected
- Categories not in the sort array are appended in their original order
- The sort operation is **O(n)** where n is the number of categories

