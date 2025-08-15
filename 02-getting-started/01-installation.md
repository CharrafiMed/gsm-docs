## Requirement

Filament v3.2.93 or later is required due to this [pull request](https://github.com/filamentphp/filament/pull/13321).

## Installation
Follow these steps to install the Global Search Modal Plugin in your Filament app:

This guide provides detailed instructions on installing and using this plugin. Should you have any inquiries, encounter a bug, require support, or wish to submit a feature request, please do not hesitate to contact me at charrafimedfilament@gmail.com

> The plugin’s master branch has supported Filament v4 for a while. I haven’t published an official v4 release yet because I am currently re-writing the entire codebase to address legacy issues. Stay tuned — the v4 release is coming very soon. 

```bash
    composer require charrafimed/global-search-modal
```

## Configuring

### plugin per panel 

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
 
public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
        ])
}
```
that's it, if you have global search enabled in your panel, so you have a fully featured experience   
@blade
<x-converge::alert type="warning">
If you are developing locally and frequently refreshing or re-seeding your database, please note that the **recent search feature** persists data into local storage. This can result in outdated URLs being used if the underlying data (such as IDs or slugs) has changed.
</x-converge::alert>
@endblade

#### Solution:
To ensure the correct URLs are being used after re-seeding or refreshing data, manually clear your browser's local storage. This will force the application to construct new URLs based on the updated data.


This issue should not occur in production environments, as data isn't frequently refreshed or re-seeded.
