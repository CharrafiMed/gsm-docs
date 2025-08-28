# Global Search Modal Plugin v4 - Modal Configuration

## Overview

The Global Search Modal Plugin v4 provides a clean, fluent API to customize modal behaviors through the `modal()` method. All configuration is done directly on the plugin instance using named parameters.

## Basic Usage

Configure the modal by calling the `modal()` method on the plugin instance:

### Large Slide-Over Modal
```php
GlobalSearchModalPlugin::make()
    ->modal(
        width: Width::FourExtraLarge,
        slideOver: true
    )
```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use Filament\Support\Enums\Width;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->modal(
                    width: Width::Large,
                    hasCloseButton: true,
                    closedByClickingAway: false,
                    closedByEscaping: true,
                    autofocused: true,
                    slideOver: false
                )
        ]);
}
```

## Configuration Options

### Modal Width

Set the modal width using Filament's `Width` enum:

```php
GlobalSearchModalPlugin::make()
    ->modal(width: Width::Large)
```

**Available Width Options:**

*Standard Sizes:*
- `Width::ExtraSmall` (`xs`)
- `Width::Small` (`sm`) 
- `Width::Medium` (`md`)
- `Width::Large` (`lg`)
- `Width::ExtraLarge` (`xl`)
- `Width::TwoExtraLarge` (`2xl`) - *default*
- `Width::ThreeExtraLarge` (`3xl`)
- `Width::FourExtraLarge` (`4xl`)
- `Width::FiveExtraLarge` (`5xl`)
- `Width::SixExtraLarge` (`6xl`)
- `Width::SevenExtraLarge` (`7xl`)

*Special Widths:*
- `Width::Full` (`full`) - Full width
- `Width::MinContent` (`min`) - Minimum content width
- `Width::MaxContent` (`max`) - Maximum content width
- `Width::FitContent` (`fit`) - Fit content width
- `Width::Prose` (`prose`) - Optimized for reading text

*Screen-Based Widths:*
- `Width::ScreenSmall` (`screen-sm`) - Small screen width
- `Width::ScreenMedium` (`screen-md`) - Medium screen width
- `Width::ScreenLarge` (`screen-lg`) - Large screen width
- `Width::ScreenExtraLarge` (`screen-xl`) - Extra large screen width
- `Width::ScreenTwoExtraLarge` (`screen-2xl`) - 2XL screen width
- `Width::Screen` (`screen`) - Full screen width

### Close Button

Enable or disable the close button:

```php
GlobalSearchModalPlugin::make()
    ->modal(hasCloseButton: true) // Default: true
```

To disable the close button:

```php
GlobalSearchModalPlugin::make()
    ->modal(hasCloseButton: false)
```

### Close by Clicking Away

Control whether the modal closes when clicking outside of it:

```php
GlobalSearchModalPlugin::make()
    ->modal(closedByClickingAway: true) // Default: true
```

To prevent closing by clicking away:

```php
GlobalSearchModalPlugin::make()
    ->modal(closedByClickingAway: false)
```

### Close by Escaping

Control whether the modal closes when pressing the Escape key:

```php
GlobalSearchModalPlugin::make()
    ->modal(closedByEscaping: true) // Default: true
```

To disable close by escaping:

```php
GlobalSearchModalPlugin::make()
    ->modal(closedByEscaping: false)
```

### Autofocus

Control whether the modal automatically focuses when opened:

```php
GlobalSearchModalPlugin::make()
    ->modal(autofocused: true) // Default: true
```

To disable autofocus:

```php
GlobalSearchModalPlugin::make()
    ->modal(autofocused: false)
```

### Slide Over Modal

Enable slide-over behavior instead of centered modal:

```php
GlobalSearchModalPlugin::make()
    ->modal(slideOver: true) // Default: false
```

## Individual Configuration Examples

### Configure Only Width
```php
GlobalSearchModalPlugin::make()
    ->modal(width: Width::Medium)
```

### Configure Only Close Behaviors
```php
GlobalSearchModalPlugin::make()
    ->modal(
        closedByClickingAway: false,
        closedByEscaping: false
    )
```

### Enable Slide Over with Close Button
```php
GlobalSearchModalPlugin::make()
    ->modal(
        slideOver: true,
        hasCloseButton: true
    )
```

## Complete Configuration Example

Here's a comprehensive example showing all configuration options:

```php
use CharrafiMed\GlobalSearchModal\GlobalSearchModalPlugin;
use Filament\Support\Enums\Width;

public function panel(Panel $panel): Panel
{
    return $panel
        ->plugins([
            GlobalSearchModalPlugin::make()
                ->modal(
                    width: Width::Large,
                    hasCloseButton: true,
                    closedByClickingAway: false,
                    closedByEscaping: true,
                    autofocused: true,
                    slideOver: false
                )
        ]);
}
```
