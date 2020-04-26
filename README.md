# BelongsTo Field with Dependency

## Installation

You can install the package in to a Laravel app that uses [Nova](https://nova.laravel.com) via composer:

```bash
composer require everestmx/nova-belongsto-depend
```

Use this field in your Nova Resource

```php

use Everestmx\NovaBelongsToDepend\NovaBelongsToDepend;

public function fields(Request $request)
{
    return [
        ID::make()->sortable(),
        Text::make('Name')->rules('required', 'max:255'),

        NovaBelongsToDepend::make('Company')
            ->placeholder('Optional Placeholder') // Add this just if you want to customize the placeholder
            ->options(\App\Company::all()),
        NovaBelongsToDepend::make('Department')
            ->placeholder('Optional Placeholder') // Add this just if you want to customize the placeholder
            ->optionsResolve(function ($company) {
                // Reduce the amount of unnecessary data sent
                return $company->departments()->get(['id','name']);
            })
            ->dependsOn('Company'),
        NovaBelongsToDepend::make('Location')
            ->placeholder('Optional Placeholder') // Add this just if you want to customize the placeholder
            ->optionsResolve(function ($company) {
                // Reduce the amount of unnecessary data sent
                return $company->locations()->get(['id','name']);
            })
            ->fallback(
                Text::make('Location Name')->rules('required', 'max:255'),
            )
            ->hideLinkToResourceFromDetail()
            ->hideLinkToResourceFromIndex()
            ->nullable()
            ->dependsOn('Company'),

    ];
}
```

## Options

```placeholder('Optional Placeholder') ```

```openDirection('top') ```
See options values from [vue-multiselect ](https://vue-multiselect.js.org/#sub-props)

## Translation

The following strings are translatable (add then in your language file located in resources/lang/vendor/nova/\*.json).

-   'Oops! No elements found. Consider changing the search query.'
-   'List is empty'
-   'Select'
-   'Press enter to select'
-   'Selected'
-   'Press enter to remove'

## Sample

[Demo Project](https://github.com/orlyapps/laravel-nova-demo)

-   Warehouse hasMany Articles
-   Articles belongsToMany Suppliers
-   Suppliers belongsToMany Articles

1. Select a **Warehouse** and get all articles of the warehouse
2. Select a **Article** and get all suppliers who has this article

```php
public function fields(Request $request)
{
    return [
        ID::make()->sortable(),
        Text::make('Name')->rules('required', 'max:255'),

        NovaBelongsToDepend::make('Warehouse')
        ->options(\App\Warehouse::all())
        ->rules('required'),
        NovaBelongsToDepend::make('Article')
            ->optionsResolve(function ($warehouse) {
                return $warehouse->articles;
            })
            ->dependsOn('Warehouse')
            ->rules('required'),
        NovaBelongsToDepend::make('Supplier')
            ->optionsResolve(function ($article) {
                return \App\Supplier::whereHas('articles', function ($q) use ($article) {
                    $q->where('article_id', $article->id);
                })->get();
            })
            ->dependsOn('Article')
            ->rules('required'),


    ];
}
```

### Security

If you discover any security related issues, please email info@orlyapps.de instead of using the issue tracker.

## Credits

-   [Orlyapps](https://github.com/orlyapps)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
