# Sample usage of `gettext` function

- [GetText](https://en.wikipedia.org/wiki/Gettext) @ Wikipedia
- [GetText](https://www.php.net/manual/en/function.gettext.php) @ PHP Manual

## How to install `gettext` extension

To use `gettext` PHP function, the `gettext` extension module and it's dependencies must be installed and loaded.

1. Install dependencies of the extension. Such as `gettext-dev` by `apk add gettext-dev`.
2. Install `gettext` extension by `docker-php-ext-install gettext`.
3. Reboot the system or reload PHP service to take effect.

- Be sure that `gettext` is in the list of loaded modules (`php -m`)
- See: [Dockerfile](./Dockerfile)

## How to use `gettext` function

1. Specify the directory of the dictionaries.
2. Use "`gettext("hello");`" or it's alias "`_("hello");`" function.
3. The return value will be the translated text according to the locale.

```php
<?php
// Set your locale if necessary
putenv('LC_ALL=ja_JP');
setlocale(LC_ALL, 'ja_JP');

// File name of the dictionary
$domain = "messages";
// Base dir of the dictionaries
bindtextdomain($domain, "./locale/");
// Set the default domain
textdomain($domain);

// Get the according text from the locale dictionary
echo _("greeting");

// Output
// こんにちは世界
```

## How to create translation files for `gettext`

To use `gettext` function in PHP, you'll need 2 files for each language to translate the string; `messages.po` and `messages.mo`.

The **`.po` is a base text file of the dictionary** before compiling. And the **`.mo` file is the compiled dictionary**. The `.po` file is also called `.pot`, Portable Object Template, and `.mo` is also called a `Machine Object` file.

Each language dictionary should be placed under `[locale]/LC_MESSAGES/`.

- Ex.
  - JA: `./locale/ja_JP/LC_MESSAGES/messages.po`
  - EN: `./locale/en_US/LC_MESSAGES/messages.po`

## Basic directory structure

```text
.
├── sample.php
├── locale
│   ├── en_US
│   │   └── LC_MESSAGES
│   │       ├── messages.mo ... Compiled English dictionary
│   │       └── messages.po ... Keyword-English pairs to be compiled
│   └── ja_JP
│       └── LC_MESSAGES
│           ├── messages.mo ... Compiled Japanese dictionary
│           └── messages.po ... Keyword-Japanese pairs to be compiled
└── messages.po ............... Template of the keyword paris
```

### How to create/generate the `.po` file

The `.po` file can be auto-generated by the `xgettext` shell-command.

```shellsession
/ # cd /app

/app # ls
sample.php

/app # xgettext sample.php

/app # ls
messages.po  sample.php
```

The `xgettext` command will analyze the PHP source. It will;

1. Detects the file name of the dictionary defined in `bindtextdomain($domain, $directory)`.
2. Lists up all the strings used in `gettext()` or `_()` as `msgid`.
3. Generates a `<domain>.po` file.

The generated `<domain>.po` file will be the template for each dictionary.

- Ex: `messages.po`

    ```conf
    # SOME DESCRIPTIVE TITLE.
    # Copyright (C) YEAR THE PACKAGE'S COPYRIGHT HOLDER
    # This file is distributed under the same license as the PACKAGE package.
    # FIRST AUTHOR <EMAIL@ADDRESS>, YEAR.
    #
    #, fuzzy
    msgid ""
    msgstr ""
    "Project-Id-Version: PACKAGE VERSION\n"
    "Report-Msgid-Bugs-To: \n"
    "POT-Creation-Date: 2020-08-24 11:50+0900\n"
    "PO-Revision-Date: YEAR-MO-DA HO:MI+ZONE\n"
    "Last-Translator: FULL NAME <EMAIL@ADDRESS>\n"
    "Language-Team: LANGUAGE <LL@li.org>\n"
    "Language: \n"
    "MIME-Version: 1.0\n"
    "Content-Type: text/plain; charset=CHARSET\n"
    "Content-Transfer-Encoding: 8bit\n"

    #: sample.php:13
    msgid "greeting"
    msgstr ""
    ```

Before anything, change "`Content-Type: text/plain; charset=CHARSET\n`" to "`Content-Type: text/plain; charset=utf-8\n`". Then, re-write the other basic information that are in capital letters which describes about the dictionary.

Basically, the `.po` file is a set of Keyword-Translation pairs. Such as `msgid` and `msgstr`.

The `msgid` will be the argument of `gettext($msgid)` or `_($msgid)` function and the return value will be the `msgstr`.

```conf
msgid "greeting"
msgstr "Hello, World!"
```

The above pair for example will return as below.

```php
echo gettext('greeting');
// Output
// Hello, World!
```

### Create Translation File (`.po` file) for Each Language

```shellsession
$ # Create the directories to place the dictionaries
$ mkdir -p locale/ja_JP/LC_MESSAGES/
$ mkdir -p locale/en_US/LC_MESSAGES/

$ # Copy the template to each directories
$ cp message.po locale/ja_JP/LC_MESSAGES/messages.po
$ cp message.po locale/en_US/LC_MESSAGES/messages.po
```

### How To Compile The Dictionary (Convert `.po` -> `.mo`)

To compile the `.po` to `messages.mo` file, use `msgfmt` command.

```shellsession
$ cd /app/locale/ja_JP/LC_MESSAGES/
$ ls
messages.po
$ msgfmt ./messages.po
$ ls
messages.mo   messages.po

$ cd /app/locale/en_US/LC_MESSAGES/
$ ls
messages.po
$ msgfmt ./messages.po
$ ls
messages.mo   messages.po
```