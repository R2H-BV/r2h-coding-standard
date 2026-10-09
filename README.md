# R2H Coding Standard
The opiniated R2H coding standard. This package includes a codesniffer standard
and a Laravel Pint preset.

## Installing
`composer require --dev r2h/r2h-coding-standard`

## Usage
### PHPCS
Assuming you've setup our standard as a preset.
`./vendor/bin/phpcs --standard=./ruleset.xml ./`

### Laravel Pint
`./vendor/bin/pint --config vendor/r2h/r2h-coding-standard/pint.json`

### Laravel Boost
This package ships AI guidelines for [Laravel Boost](https://github.com/laravel/boost)
in `resources/boost/guidelines/`. Boost discovers them automatically as long as
this package is a direct dependency of your application (in `require` or
`require-dev` of your `composer.json`).

Run `php artisan boost:install` (or `php artisan boost:update` when Boost is
already installed) and select `r2h/r2h-coding-standard` in the list of
third-party packages. The guidelines are then added to the Boost guidelines
of your application (e.g. `CLAUDE.md`, `AGENTS.md`).

To add a new rule, add a Markdown (`.md`) or Blade (`.blade.php`) file to
`resources/boost/guidelines/`, or extend `core.md`.
