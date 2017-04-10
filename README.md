QR Code
=======

*By [endroid](http://endroid.nl/)*

[![Latest Stable Version](http://img.shields.io/packagist/v/endroid/qrcode.svg)](https://packagist.org/packages/endroid/qrcode)
[![Build Status](http://img.shields.io/travis/endroid/QrCode.svg)](http://travis-ci.org/endroid/QrCode)
[![Total Downloads](http://img.shields.io/packagist/dt/endroid/qrcode.svg)](https://packagist.org/packages/endroid/qrcode)
[![Monthly Downloads](http://img.shields.io/packagist/dm/endroid/qrcode.svg)](https://packagist.org/packages/endroid/qrcode)
[![License](http://img.shields.io/packagist/l/endroid/qrcode.svg)](https://packagist.org/packages/endroid/qrcode)

This library helps you generate QR codes.

## Installation

Use [Composer](https://getcomposer.org/) to install the library.

``` bash
$ composer require endroid/qrcode
```

## Usage

```php
use Endroid\QrCode\QrCode;

// Create a QR code
$qrCode = new QrCode('Life is too short to be generating QR codes');
$qrCode->setSize(300);

// Advanced options
$qrCode
    ->setQuietZone(2)
    ->setErrorCorrectionLevel('H')
    ->setForegroundColor(['r' => 0, 'g' => 0, 'b' => 0, 'a' => 0])
    ->setBackgroundColor(['r' => 255, 'g' => 255, 'b' => 255, 'a' => 0])
    ->setLabel('Scan the code', 16, __DIR__.'/../font/open_sans.ttf')
    ->setLogo(__DIR__.'/../logo/endroid.png', 50)
;

// now we can output the QR code
header('Content-Type: '.$qrCode->getContentType(QrCode::TYPE_PNG));
$qrCode->write(QrCode::TYPE_PNG);

// save it to a file (uses type guessing)
$qrCode->write(__DIR__.'/qrcode.png');

// create a response object
$response = new Response(
    $qrCode->write(QrCode::TYPE_SVG),
    Response::HTTP_OK,
    ['Content-Type' => $qrCode->getContentType(QrCode::TYPE_SVG)])
;
```

![QR Code](http://endroid.nl/qrcode/Life%20is%20too%20short%20to%20be%20generating%20QR%20codes.png?label=Scan%20the%20code)

## Symfony integration

Register the Symfony bundle in the kernel.

```php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = [
        // ...
        new Endroid\QrCode\Bundle\EndroidQrCodeBundle(),
    ];
}
```

The default parameters can be overridden via the configuration.  
Alpha channel available range is [0, 127] in foreground and background colors.

```yaml
endroid_qr_code:
    size: 100
    quiet_zone: 2
    error_correction_level: H
    foreground_color: { r: 0, g: 0, b: 0, a: 0 }
    background_color: { r: 255, g: 255, b: 255, a: 0 }
    label: 'Scan the code'
    label_font_size: 16
```

Now you can retrieve the factory as follows.

```php
$factory = $this->get('endroid.qrcode.factory');
$factory->createQrCode();
```

Add the following section to your routing to be able to handle QR code URLs.
This step can be skipped when you only use data URIs to display your images.

``` yml
EndroidQrCodeBundle:
    resource: "@EndroidQrCodeBundle/Controller/"
    type:     annotation
    prefix:   /qrcode
```

After installation and configuration, QR codes can be generated by appending
the QR code text to the url as mounted, followed by .png, .jpg or .gif.

## Twig extension

The bundle also provides a Twig extension for quickly generating QR code urls.
Optional parameters are extension, size, quiet zone and errorCorrectionLevel. When
a parameter is omitted, the value in the bundle configuration is used.

``` twig
<img src="{{ qrcode_url(message) }}" />
<img src="{{ qrcode_url(message, { extension: 'png' }) }}" />
<img src="{{ qrcode_url(message, { size: 150 }) }}" />
```

You can also use the data URI helper to embed the QR code within your HTML
instead of requiring a separate HTTP request to load your image.

``` twig
<img src="{{ qrcode_data_uri(message, { size: 200, quiet_zone: 2 }) }}" />
```

## Versioning

Version numbers follow the MAJOR.MINOR.PATCH scheme. Backwards compatibility
breaking changes will be kept to a minimum but be aware that these can occur.
Lock your dependencies for production and test your code when upgrading.

## License

This bundle is under the MIT license. For the full copyright and license
information please view the LICENSE file that was distributed with this source code.
