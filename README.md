## THIS PROJECT IS NO LONGER ACTIVELY MAINTAINED, USE AT YOUR OWN RISK

# Twig Markdown Extension

**Updated for Twig 2.\* - For Twig 1.\* please use version 1.0.1**

A simple and extendable twig extension for providing markdown filters, globals, tags and functions.

By default, this extension comes with [ParsedownExtra](https://github.com/erusev/parsedown-extra), but this can be easily replaced with any markdown processor or your choice by simply implementing the provided interface and passing your new implementation into the extension.

## Installation ##

You can simply install the extension package through composer.

    composer require jralph/twig-markdown

You can also add the package and the version you want to your composer.json file.

    "require": {
        "jralph/twig-markdown": "dev-master"
    }

## Setup With Twig ##

To use this extension with twig (without any additions such as TwigBridge for Laravel. _See below._), you can simply do the following.

```php
$twig = new Twig_Environment($loader);
$twig->addExtension(new Jralph\Twig\Markdown\Extension(
    new Jralph\Twig\Markdown\Parsedown\ParsedownExtraMarkdown
));
```

## Setup With TwigBridge for Laravel 5 ##

To use this plugin with [TwigBridge](https://github.com/rcrowe/TwigBridge) for Laravel, it is just as easy, but you have multiple ways of adding the extension.

__Via config/twigbridge.php__

You can add the extension directly to the `enabled` section of the `extensions` array within the `config/twigbridge.php` file. (Note, you will need to make sure that the config file has been published `php artisan vendor:publish` for this file to exist.)

```php
'extensions' => [

    'enabled' => [
        // Other TwigBridge Extensions
        new Jralph\Twig\Markdown\Extension(
            new Jralph\Twig\Markdown\Parsedown\ParsedownExtraMarkdown
        ),
    ]

]
```

__Via Twig Facade__

You can also add the extension using the `Twig` facade that TwigBridge provides.

```php
Twig::addExtension(new Jralph\Twig\Markdown\Extension(
    new Jralph\Twig\Markdown\Parsedown\ParsedownExtraMarkdown
));
```

You can add this code to your Laravel 5 install in any way you like, but we recommend using a service provider.

## Provided Functionality ##

The Twig-Markdown extension provides globals, functions, filters and tags to assist you with your markdown processing.


### Filter

_NOTE: The filter currently **DOES NOT** sanitize input, and thus could be vulnerable to XSS if user input is not pre-sanisized. If you are unable to sanitize the user data yourself, please consider using the `{% markdown %}` tag functionality instead, as this does sanitize input._

Use just like any other twig filter.

    {{ "# Some Markdown" | markdown }}
    {{ markdownVariable | markdown }}
    
    {% apply markdown %}
        # Some Markdown

        This is some simple markdown content.
        
        {{ moreMarkdown }}
    {% endapply %}

### Function

_NOTE: The function currently **DOES NOT** sanitize input, and thus could be vulnerable to XSS if user input is not pre-sanisized. If you are unable to sanitize the user data yourself, please consider using the `{% markdown %}` tag functionality instead, as this does sanitize input._

Use just like any other twig function.

    {{ markdown("# Some Markdown") }}
    {{ markdown(markdownVariable) }}

### Global

You can also use the global for direct access to the implementation of the MarkdownInterface contract.

    {% autoescape false %}
        {{ markdown.prase("# Some Markdown") }}
        {{ markdown.parse(markdownVariable) }}
    {% endautoescape %}

_Note the use of the `{% autoescape false %}`. Without this, the generated html will be escaped......which may or may not be what you are looking for._

### Tag

We also provide a handy tag for you to use if you want to write the markdown within a template.

    {% markdown %}
        # Some Markdown

        This is some simple markdown content.
        
        {{ moreMarkdown }}
    {% endmarkdown %}

## Using Another Processor

Want to use another processor other than ParsedownExtra? No problem!

Just implement the `Jralph\Twig\Markdown\Contracts\MarkdownInterface` contract, add it to the extension and you're away.

The contract requires the following methods:

- `parse($text)`;
    - This method should return the parsed `$text`.

Example using [Michelf Markdown](https://github.com/michelf/php-markdown).

```php
// MichelfMardown.php
<?php

use Jralph\Twig\Markdown\Contracts\MarkdownInterface;
use Michelf\Markdown;

class MichelfMardown implements MarkdownInterface {

    public function parse($text)
    {
        $markdown = new Markdown;

        return $markdown->transform($text);
    }

}
```

Now you have the implementation setup, just add this into the twig extension.

```php
// For plain twig.

$twig = new Twig_Environment($loader);
$twig->addExtension(new Jralph\Twig\Markdown\Extension(
    new MichelfMardown
));

// For TwigBridge

'extensions' => [

    'enabled' => [
        // Other TwigBridge Extensions
        new Jralph\Twig\Markdown\Extension(
            new MichelfMardown
        ),
    ]

]

// OR

Twig::addExtension(new Jralph\Twig\Markdown\Extension(
    new MichelfMardown
));
```

It's as simple as that!

## Contributing/Maintaining

I will do my best to keep this package up-to-date but if you notice any bugs or would like to add a feature, please feel free to submit an issue on GitHub or submit a pull request with the change your self.
