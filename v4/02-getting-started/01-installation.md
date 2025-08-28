## Requirement

Filament v4 .

## Installation
Follow these steps to install the Global Search Modal Plugin in your Filament app:



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
