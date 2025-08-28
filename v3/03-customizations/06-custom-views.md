## Custom Views
The Global Search Modal plugin provides several customization options for views, allowing you to create a more personalized search experience. Below are the methods provided by the Plugin and how to use them.

### Enable Footer Views

You can remove the footer view using the `keepFooterView` method.

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->keepFooterView(false) // Enables the footer view
        ]);
}
```
### Custom Footer View

You can set a custom footer view using the footerView method. This method accepts a view instance
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use Illuminate\Support\Facades\View;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->footerView(View::make('custom-footer-view')) // or the view helper method view 
        ]);
}
```
it will look under resources/views by default, if you want to use custom namespace u can specify the suffix like so :
```php
GlobalSearchModalPlugin::make()
                ->footerView(View::make('namespace::custom-footer-view'))
```
### Custom Not Found View
You can set a custom view for when no search results are found using the notFoundResultsView method.

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use Illuminate\Support\Facades\View;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->notFoundResultsView(View::make('custom-not-found-view'))
        ]);
}
```
### Custom Empty Query View

You can set a custom view for when the search query is empty using the `emptyQueryView` method.
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use Illuminate\Support\Facades\View;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->emptyQueryView(View::make('custom-empty-query-view')) // Sets a custom empty query view
        ]);
}
```
## Placeholder 
You can customize the placeholder text for the search input using the `placeholder` method provided by the plugin
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->placeholder('Type to search...')      
        ]);
}
```

## Search Item Tree Icon 
You can configure whether the search results are displayed with a tree icon in left or not. The following example shows how to enable or disable this feature.

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->searchItemTree(false)
        ]);
}
```
## Expanded Url Target
You can configure whether the results area is fully clickable or limited to just the title area. The following example demonstrates how to enable or disable this feature.
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->expandedUrlTarget(enabled: true)
        ]);
}

```