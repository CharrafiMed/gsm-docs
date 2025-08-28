
## Rendering Under Custom Scopes 
This plugin allows you to customize the rendering of components based on specific scopes.
### Example Usage
To define a custom scope for rendering, use the scopes() method. For example, you can pass a resource:

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->scopes(UserResource::class)
    ]);
}
```

Here, ``UserResource::class`` is passed as a scope, and the plugin will render only when this scope is active. 
#### Passing Multiple Scopes
You can also pass multiple scopes as an array:

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->scopes([UserResource::class, PostResource::class])
    ]);
}
```