# Локализация

- [Введение](#introduction)
- [Определение строк перевода](#defining-translation-strings)
    - [Использование кратких ключей](#using-short-keys)
    - [Использование строк перевода как ключей](#using-translation-strings-as-keys)
- [Получение строк перевода](#retrieving-translation-strings)
    - [Подстановка параметров в строках перевода](#replacing-parameters-in-translation-strings)
    - [Плюрализация](#pluralization)
- [Переопределение языковых файлов пакета](#overriding-package-language-files)

<a name="introduction"></a>
## Введение

Возможности локализации Laravel предоставляют удобный способ получения строк на нескольких языках, что позволяет легко организовать поддержку нескольких языков вашим приложением. Языковые строки хранятся в файлах, располагающихся внутри директории `resources/lang`. В ней должны располагаться подпапки для каждого языка, поддерживаемого вашим приложением:

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

Все языковые файлы возвращают простой массив ключей и строк. Например:

    <?php

    return [
        'welcome' => 'Welcome to our application'
    ];

### Настройка локали

Стандартный язык вашего приложения указан в конфигурационном файле `config/app.php`. Конечно же, вы можете изменить это значение на основе потребностей вашего приложения. Вы также можете изменить активный язык в процессе работы приложения с помощью метода `setLocale` фасада `App`:

    Route::get('welcome/{locale}', function ($locale) {
        App::setLocale($locale);

        //
    });

Вы можете настроить "запасной язык", который будет использоваться в случаях, когда активный язык не содержит заданной строки перевода. Как и стандартный язык, запасной язык также настраивается в конфигурационном файле `config/app.php`:

    'fallback_locale' => 'en',

#### Определение текущей локали

Вы можете использовать методы `getLocale` и `isLocale` фасада `App` для определения текущей локали или проверки совпадения локали с заданным значением:

    $locale = App::getLocale();

    if (App::isLocale('en')) {
        //
    }

<a name="defining-translation-strings"></a>
## Defining Translation Strings

<a name="using-short-keys"></a>
### Using Short Keys

Typically, translation strings are stored in files within the `resources/lang` directory. Within this directory there should be a subdirectory for each language supported by the application:

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

All language files simply return an array of keyed strings. For example:

    <?php

    // resources/lang/en/messages.php

    return [
        'welcome' => 'Welcome to our application'
    ];

<a name="using-translation-strings-as-keys"></a>
### Using Translation Strings As Keys

For applications with heavy translation requirements, defining every string with a "short key" can become quickly confusing when referencing them in your views. For this reason, Laravel also provides support for defining translation strings using the "default" translation of the string as the key.

Translation files that use translation strings as keys are stored as JSON files in the `resources/lang` directory. For example, if your application has a Spanish translation, you should create a `resources/lang/es.json` file:

    {
        "I love programming.": "Me encanta la programación."
    }

<a name="retrieving-translation-strings"></a>
## Retrieving Translation Strings

You may retrieve lines from language files using the `__` helper function. The `__` method accepts the file and key of the translation string as its first argument. For example, let's retrieve the `welcome` translation string from the `resources/lang/messages.php` language file:

    echo __('messages.welcome');

    echo __('I love programming.');

Of course if you are using the [Blade templating engine](/docs/{{version}}/blade), you may use the `{{ }}` syntax to echo the translation string or use the `@lang` directive:

    {{ __('messages.welcome') }}

    @lang('messages.welcome')

If the specified translation string does not exist, the `__` function will simply return the translation string key. So, using the example above, the `__` function would return `messages.welcome` if the translation string does not exist.

<a name="replacing-parameters-in-translation-strings"></a>
### Replacing Parameters In Translation Strings

If you wish, you may define place-holders in your translation strings. All place-holders are prefixed with a `:`. For example, you may define a welcome message with a place-holder name:

    'welcome' => 'Welcome, :name',

To replace the place-holders when retrieving a translation string, pass an array of replacements as the second argument to the `__` function:

    echo __('messages.welcome', ['name' => 'dayle']);

If your place-holder contains all capital letters, or only has its first letter capitalized, the translated value will be capitalized accordingly:

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle


<a name="pluralization"></a>
### Pluralization

Pluralization is a complex problem, as different languages have a variety of complex rules for pluralization. By using a "pipe" character, you may distinguish singular and plural forms of a string:

    'apples' => 'There is one apple|There are many apples',

You may even create more complex pluralization rules which specify translation strings for multiple number ranges:

    'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',

After defining a translation string that has pluralization options, you may use the `trans_choice` function to retrieve the line for a given "count". In this example, since the count is greater than one, the plural form of the translation string is returned:

    echo trans_choice('messages.apples', 10);

<a name="overriding-package-language-files"></a>
## Overriding Package Language Files

Some packages may ship with their own language files. Instead of changing the package's core files to tweak these lines, you may override them by placing files in the `resources/lang/vendor/{package}/{locale}` directory.

So, for example, if you need to override the English translation strings in `messages.php` for a package named `skyrim/hearthfire`, you should place a language file at: `resources/lang/vendor/hearthfire/en/messages.php`. Within this file, you should only define the translation strings you wish to override. Any translation strings you don't override will still be loaded from the package's original language files.
