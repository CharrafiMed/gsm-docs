






## lazy loading issues 
If you have disabled lazy loading in your service provider, as shown below:

ex:
```php
<?php

namespace App\Providers;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Http\Resources\Json\JsonResource;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        
        Model::preventLazyLoading(); <--
    }
}

```
You might encounter the ``Illuminate\Database\LazyLoadingViolationException`` when attempting to access attributes tied to a relationship without explicitly loading the relationship.

For example, searching for a post and then trying to access its `category` attribute will throw this exception unless the relationship is preloaded.

To resolve this, make sure to load the relationship when defining the query, such as in a global search feature. Use the with method to specify related models to load:
```php
    public static function getGlobalSearchEloquentQuery(): Builder
    {
        return parent::getGlobalSearchEloquentQuery()->with(['category']);
    }
```
Repeat this process for each resource with attributes linked to a relationship.

For more details, refer to the  [filament global search feature](https://filamentphp.com/docs/3.x/panels/resources/global-search#adding-extra-details-to-global-search-results)

## Troubleshooting

In some scenarios, this plugin can lead to unexpected errors when a user is logged out. So far, we've discovered one solution, which involves adding [scopes](##rendering-under-custom-scopes) to exclude pages that should be visible when a user is logged out, such as the sign-in or sign-out pages.

### example

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->scopes([UserResource::class, PostResource::class,Filament\Pages\Dashboard::class,......])
    ]);
}
```
 needs a fully featured framework for managing docs  [see converge](https://convergephp.com/)

However, this approach can be cumbersome, especially if you have a large project with multiple resources and pages. Additionally, in SPA mode, this solution can cause Filament inputs to behave oddly in the UI.
