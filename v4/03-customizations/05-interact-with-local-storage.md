## local storage    
The Global Search Modal plugin includes functionalities for interacting with local storage. This allows the plugin to retain recent searches, and favorite searches, and organize search items into groups. Below are the methods provided by the `CanInteractWithLocalStorage` trait and how to use them.
### Maximum Items Allowed
by default it keeps track of 10 items of favorites and 10 of recent, You can set the maximum number of items allowed in the local storage using the `localStorageMaxItemsAllowed` method. This method accepts an integer.
**Notice** If you set 8 for example it will allow 8 items in local storage and keep track of 8 recent search  

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->localStorageMaxItemsAllowed(20) // sets maximum items to 50
        ]);
}
```
### Retain Recent Searches if Favorited

You can enable or disable the retention of recent searches if they are also marked as favorites using the RetainRecentIfFavorite method.
in other words, if try to mark a recent to favorites it will removed from the recent.
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->RetainRecentIfFavorite(true) // enables retention of recent searches if they are favorites
        ]);
}
```
### Associate Items with Their Groups
You can enable the association of items with their groups using the associateItemsWithTheirGroups method.
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;

public function panel(Panel $panel): Panel
{
    return $panel
        ...
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->associateItemsWithTheirGroups() // enables association of items with groups
        ]);
}

```
