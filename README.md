[![Latest StableVersion](https://poser.pugx.org/toin0u/geocoder-laravel/v/stable.png)](https://packagist.org/packages/toin0u/geocoder-laravel)
[![Total Downloads](https://poser.pugx.org/toin0u/geocoder-laravel/downloads.png)](https://packagist.org/packages/toin0u/geocoder-laravel)
[![Build Status](https://ci.genealabs.com/build-status/image/1)](https://ci.genealabs.com/build-status/view/1)
[Code Coverate Report](https://ci.genealabs.com/coverage/1)

# Geocoder for Laravel

> If you still use **Laravel 4**, please check out the `0.4.x` branch
 [here](https://github.com/geocoder-php/GeocoderLaravel/tree/0.4.x).

**Version 2.0.0 is a backwards-compatibility-breaking update. Please review
 this documentation, especially the _Usage_ section before installing.**

This package allows you to use [**Geocoder**](http://geocoder-php.org/Geocoder/)
 in [**Laravel 5**](http://laravel.com/).

## Requirements
- PHP >= 7.0.0
- Laravel >= 5.0

## Installation
1. Install the package via composer:
 ```sh
composer require toin0u/geocoder-laravel
```

2. Find the `providers` array key in `config/app.php` and register the **Geocoder Service Provider**:
 ```php
// 'providers' => [
    Geocoder\Laravel\Providers\GeocoderService::class,
// ];
```

## Upgrading
### 1.x to 2.x
Update your composer.json file:
```json
    "toin0u/geocoder-laravel": "^2.0",
```

The one change to keep in mind here is that the results returned from
 `Geocoder for Laravel` are now using the Laravel-native Collections class
 instead of returning an instance of `AddressCollection`. This should provide
 greater versatility in manipulation of the results, and be inline with
 expectations for working with Laravel. The existing `AddressCollection`
 methods should map strait over to Laravel's `Collection` methods. But be sure
 to double-check your results, if you have been using `count()`,
 `first()`, `isEmpty()`, `slice()`, `has()`, `get()`, or `all()` on your results.

Also, `getProviders()` now returns a Laravel Collection instead of an array.

**Alert:** if you have been using the `getIterator()` method, it is no longer
 needed. Simply iterate over your results as you would any other Laravel
 collection.

**Deprecated:** the `all()` method on the geocoder is being deprecated in favor
 of using `get()`, which will return a Laravel Collection. You can then run
 `all()` on that. This method will be removed in version 3.0.0.

**Added:** this version introduces a new way to create more complex queries:
  - geocodeQuery()
  - reverseQuery()

 Please see the [Geocoder documentation](https://github.com/geocoder-php/Geocoder)
 for more details.

### 0.x to 1.x
If you are upgrading from a pre-1.x version of this package, please keep the
 following things in mind:

1. Update your composer.json file as follows:

    ```json
    "toin0u/geocoder-laravel": "^1.0",
    ```

2. Remove your `config/geocoder.php` configuration file. (If you need to customize it, follow the configuration instructions below.)
3. Remove any Geocoder alias in the aliases section of your `config/app.php`. (This package auto-registers the aliases.)
4. Update the service provider entry in your `config/app.php` to read:

    ```php
    Geocoder\Laravel\Providers\GeocoderService::class,
    ```

5. If you are using the facade in your code, you have two options:
    1. Replace the facades `Geocoder::` (and remove the corresponding `use` statements) with `app('geocoder')->`.
    2. Update the `use` statements to the following:

        ```php
        use Geocoder\Laravel\Facades\Geocoder;
        ```

6. Update your query statements to use `->get()` (to retrieve a collection of
 GeoCoder objects) or `->all()` (to retrieve an array of arrays), then iterate
 to process each result.

## Configuration
Pay special attention to the language and region values if you are using them.
 For example, the GoogleMaps provider uses TLDs for region values, and the
 following for language values: https://developers.google.com/maps/faq#languagesupport.

Further, a special note on the GoogleMaps provider: if you are using an API key,
 you must also use set HTTPS to true. (Best is to leave it true always, unless
 there is a special requirement not to.)

See the [Geocoder documentation](http://geocoder-php.org/Geocoder/) for a list
 of available adapters and providers.

### Default Settings
If you are upgrading and do not update your config file with the `cache-duraction`
 variable, cache will by default be disabled (it will have a `0` cache duration).
 The default cache duration provided by the config file is `999999999` minutes,
 essentially forever.

By default, the configuration specifies a Chain Provider as the first provider,
 containing GoogleMaps and FreeGeoIp providers. The first to return a result
 will be returned. After the Chain Provider, we have added the BingMaps provider
 for use in specific situations (providers contained in the Chain provider will
 be run by default, providers not in the Chain provider need to be called
 explicitly). The second GoogleMaps Provider outside of the Chain Provider is
 there just to illustrate this point (and is used by the PHPUnit tests).
```php
use Http\Client\Curl\Client;
use Geocoder\Provider\BingMaps\BingMaps;
use Geocoder\Provider\Chain\Chain;
use Geocoder\Provider\FreeGeoIp\FreeGeoIp;
use Geocoder\Provider\GoogleMaps\GoogleMaps;

return [
    'cache-duraction' => 999999999,
    'providers' => [
        Chain::class => [
            GoogleMaps::class => [
                'en-US',
                env('GOOGLE_MAPS_API_KEY'),
            ],
            FreeGeoIp::class  => [],
        ],
        BingMaps::class => [
            'en-US',
            env('BING_MAPS_API_KEY'),
        ],
        GoogleMaps::class => [
            'us',
            env('GOOGLE_MAPS_API_KEY'),
        ],
    ],
    'adapter'  => Client::class,
];
```

### Customization
If you would like to make changes to the default configuration, publish and
 edit the configuration file:
```sh
php artisan vendor:publish --provider="Geocoder\Laravel\Providers\GeocoderService" --tag="config"
```

## Usage
The service provider initializes the `geocoder` service, accessible via the
 facade `Geocoder::...` or the application helper `app('geocoder')->...`.

### Geocoding Addresses
#### Get Collection of Addresses
```php
app('geocoder')->geocode('Los Angeles, CA')->get();
```

#### Reverse-Geocoding
```php
app('geocoder')->reverse(43.882587,-103.454067)->get();
```

#### Dumping Results
```php
app('geocoder')->geocode('Los Angeles, CA')->dump('kml');
```

## Changelog
https://github.com/geocoder-php/GeocoderLaravel/blob/master/CHANGELOG.md

## Support
If you are experiencing difficulties, please please open an issue on GitHub:
 https://github.com/geocoder-php/GeocoderLaravel/issues.

## Contributor Code of Conduct
Please note that this project is released with a
 [Contributor Code of Conduct](https://github.com/geocoder-php/Geocoder#contributor-code-of-conduct).
 By participating in this project you agree to abide by its terms.

## License
GeocoderLaravel is released under the MIT License. See the bundled
 [LICENSE](https://github.com/geocoder-php/GeocoderLaravel/blob/master/LICENSE)
 file for details.
