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
