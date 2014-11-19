# SilverStripe External URL Field

Provides a `DBField` and `FormField` for handling external URLs.

Validate and tidy urls as they are captured from users. Configuration is highly flexible. Makes use of php's `parse_url` to do the actual work.

## Installation

```sh
composer require burnbright/silverstripe-externalurlfield *@stable
```

## Requirements

Makes use of the `http_build_url` from the [PECL pecl_http library](http://php.net/manual/en/ref.http.php). However, it also requires a [PHP fallback/shim/polyfill](https://github.com/jakeasmith/http_build_url) via composer. The composer replacement will check for the existance of `http_build_url`.

## DataObject / Template Usage

Handled by `ExternalURL` class (Varchar).

```php
class MyDataObject extends DataObject {

    private static $db = array(
        'Website' => 'ExternalURL'
    );

}
```

```
<% with $MyDataObject %>
    <p>Website: $Website</p>
    <p>Website Nice: $Website.Nice</p>
    <p>Website Domain: $Website.Domain</p>
    <p>Website Domain No WWW: $Website.Domain.NoWWW</p>
<% end_with %>
```

Given the url `http://username:password@www.hostname.com:81/path?arg=value#anchor`, the above produces:
```
Website: http://username:password@www.hostname.com:81/path?arg=value#anchor
Website Nice: www.hostname.com/path
Website Domain: www.hostname.com
Website Domain No WWW: hostname.com
```

## Form Usage

Handled by `ExternalURLField` (FormField).

Validation is handled by the html5 pattern attribute, and also server side by [a more robust regular expression](https://gist.github.com/dperini/729294).
The field uses the html5 `type="url"` attribute.

You can configure various parts of the url to be required or stripped out, or untouched upon saving.

 * **true**: enforce requirement
 * **false**: strip out
 * **null**: no action if present or not

```php

//default
$websitefield = new ExternalURLField('Website');

//set options (with defaults shown)
$websitefield->setConfig(array(
    'requirements' => array(
        'protocol' => true,
        'username' => false,
        'password' => false,
        'subdomain' => null,
        'hostname' => true,
        'port' => null,
        'path' => null,
        'query' => null,
        'fragment' => null
    )
));
```