#DataURI Twig Extension

[![Build Status](https://secure.travis-ci.org/romainneutron/TwigExtension-DataUri.png?branch=master)](http://travis-ci.org/romainneutron/TwigExtension-DataUri)

This is an extension for Twig Templating engine (http://twig.sensiolabs.org/)

This extension makes easy to use the dataURI scheme as specified in RFC 2397
(see https://www.ietf.org/rfc/rfc2397.txt).

Be carefull, as explained in the RFC, it can not use as a replacement for
traditionnal sources. DataURI scheme is length limited and can not handle all
files / ressources.

##Install

This extension requires PHP 5.3.2.

Download composer following instructions at http://getcomposer.org/, then add
this to your ``composer.json`` :

```json
{
    "require": {
        "data-uri/twig-extension": "dev-master"
    }
}

```

Then, register the extension in your twig environment :

```php
<?php
$twig->addExtension(new \DataURI\TwigExtension());
```

##Usage

DataURI etensions works with, **stream ressource**, **scalar value**,
a **binary string**, or a **pathname** for a file.

###Pathname

```php
<?php
$twig->render('<img title="hello" src="{{ image | dataUri }}" />', array('image' => '/path/to/image.jpg'));
```
will render something like :

```html
<img title="hello" src="data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAAB...SUhEU==" />
```

###Ressource

```php
<?php
$file = fopen('/path/to/image.jpg', 'r');
$twig->render('<img title="hello" src="{{ image | dataUri }}" />', array('image' => $file));
```

###Binary string

```php
<?php
$file = file_get_contenst('/path/to/image.jpg');
$twig->render('<img title="hello" src="{{ image | dataUri(true, 'image/jpeg') }}" />', array('image' => $file));
```

##Options :

DataUri can take up to 3 parameters :

``dataUri(strictMode, mimeType, parameters)``

###Unlock data length restriction

As said above, the RFC is quite strict on the output max length. By default, the
extension is 100% RFC compliant and the extension will log warnings if you render
data too large, but you can unlock the limit with the first option of the filter:

```php
<?php
$twig->addExtension(new \DataURI\TwigExtension());
$twig->render('<img title="hello" src="{{ image | dataUri(false) }}" />', array('image' => '/path/to/BIGPICTURE.jpg'));

```

**note** : If you display errors, warning message will result in Twig throws
Twig_Error_Runtime exception.

###Exemple Mimetype

```php
<?php
$file = fopen('bunny.png', 'r');
$twig->render("{{ file | dataUri(false, 'image/png') }}", array('file' => $file));
```

will render something like :

```
data:image/png;base64,oAYTUKHJKPPZ...F873=/SO
```

###Exemple Parameters

```php
<?php
$json = '{"Hello":"World !"}';
$twig->render( '{{ json | dataUri(false, "application/json", {"charset":"utf-8"}) }}', array('json' => $json));
```

will render :

```
data:application/json;charset=utf-8,%7B%22Hello%22%3A%22World%20%21%22%7D
```

##License

This extension is released under the MIT License
