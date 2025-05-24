## query highlightation  
You can enable or disable the highlighting of query matches using the `->highlighter()` method. By default, highlighting is enabled.

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->highlighter(false) // disable highlighting
        ]);
}
```
### Passing Styles
To customize the styles for the highlighted text, you can use the highlightQueryStyles method. This method accepts a string or an array of styles.

#### Example With Array
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->highlightQueryStyles([
                    'background-color' => 'yellow',
                    'font-weight' => 'bold',
                ])
        ]);
}
```
#### Example With Raw String

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                    ->highlightQueryStyles('background-color: yellow; font-weight: bold;') // Custom styles

        ]);
}
```