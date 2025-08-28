
## Customize modal behaviors

###  Close by escaping
by default this plugin comes with close-by escaping enabled, if  you want to  customize the close-by escaping behavior you can do it like so : 
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
 
public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->closeByEscaping(enabled: false)
        ])
}
```
###  Close by clicking away
by default this plugin comes with a modal that can close by clicking away enabled, if  you want to  customize the close by clicky away behavior you can do it like so : 
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
 
public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->closeByClickingAway(enabled: false)
        ])
}
```
###  Close button 
By default, the plugin does not include a close button. To add a close button:

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
 
public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->closeButton(enabled: true)
        ])
}
```

###  Swappable on mobile
To  disable swiping to close on mobile:


```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
 
public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->SwappableOnMobile(enabled: false)
        ])
}
```
### Modal slide over 
by default this plugin comes with a modal centered to the center, however, if  you want to make this modal slide over, you can do it like so : 
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
 
public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->slideOver()
        ])
}
```
### max width 
by default this plugin comes with a modal of max-width 2xl (corresponding to tailwind standard), however, if  you want to customize the  modal max-width, you can do it like so :
you can use the filament core `maxWidth` Enums under namespace ``Filament\Support\Enums\MaxWidth`` 
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use Filament\Support\Enums\MaxWidth;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->maxWidth(MaxWidth::TwoExtraLarge) // for example 
        ])
}
```


available options are :

- ExtraSmall
- Small
- Medium
- Large
- ExtraLarge
- TwoExtraLarge
- ThreeExtraLarge
- FourExtraLarge
- FiveExtraLarge
    ...
### modal position 
The Global Search Modal Plugin allows you to customize the modal's position using the `position` method. You can define the position of the modal by specifying the top, right, left, right, left, and bottom values. The method supports two formats for specifying the position: numeric values with units and strings with units.

#### Example: Customizing the Position

To customize the modal's position, use the `position` method within the `GlobalSearchModalPlugin` instance. You can specify the top and bottom values using the `top` and `right` methods, respectively. The two supported formats are:
1. **Numeric Values with Units**: Specify the position using a numeric value followed by a unit (e.g., `100, 'px'`).
2. **String with Units**: Specify the position directly as a string (e.g., `"30px"`).
#### Usage Example

Here is an example of how to customize the modal's position:
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use CharrafiMed\GlobalSearchModal\Customization\Position;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
        GlobalSearchModalPlugin::make()
            ->position(
                fn (Position $position) => $position
                    ->top(100, 'px')     // Numeric value with unit
                    ->right('30rem')     // String with unit
            )
    ]);
}
```
Both formats are supported, and you can use them interchangeably based on your preference.

**Tip**: This method uses native CSS styling, so you can use any CSS unit with any float value.
