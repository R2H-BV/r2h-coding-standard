# R2H Coding Standard

These rules are part of the R2H coding standard and apply to every R2H Laravel application.

## Eloquent Model Docblocks

- Every Eloquent model must have a class docblock that documents **every column of the model's database table** as a `@property-read` annotation. Do not use `@property` or `@property-write` for database columns.
- Check the migrations (or the database schema) to find all columns, including `id`, foreign keys, timestamps (`created_at`, `updated_at`) and `deleted_at` for soft-deleting models.
- Type each property with the PHP type it has after casting (e.g. `\Carbon\CarbonImmutable` or `\Illuminate\Support\Carbon` for dates, `bool` for boolean casts, enum classes for enum casts, `array` for JSON/array casts). Mark nullable columns as nullable, e.g. `?string`.
- Every Eloquent model docblock must also contain the Builder mixin: `@mixin \Illuminate\Database\Eloquent\Builder<static>`.
- Keep the docblock in sync with the database: when you add, rename, remove or change a column in a migration, update the `@property-read` annotations of the related model in the same change.

Example:

```php
<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

/**
 * @property-read int $id
 * @property-read int $user_id
 * @property-read string $title
 * @property-read ?string $description
 * @property-read bool $is_published
 * @property-read \Illuminate\Support\Carbon|null $published_at
 * @property-read \Illuminate\Support\Carbon $created_at
 * @property-read \Illuminate\Support\Carbon $updated_at
 * @property-read \Illuminate\Support\Carbon|null $deleted_at
 *
 * @mixin \Illuminate\Database\Eloquent\Builder<static>
 */
class Post extends Model
{
    use SoftDeletes;

    protected function casts(): array
    {
        return [
            'is_published' => 'boolean',
            'published_at' => 'datetime',
        ];
    }
}
```

## Checking PHP Code with Pint, PHPCS and PHPStan

- Every PHP file you created or changed must be checked with **Laravel Pint**, **PHPCS** and **PHPStan** before the change is considered done. Fix every reported error and warning; a change is only done when all three tools pass.
- Only check the files and directories you touched; do not run the tools on the whole project unless asked.
- Run the tools in this order, because each step can change the code the next one checks:
  1. `pint` to format the code.
  2. `phpcbf` to fix PHPCS violations that can be fixed automatically, then `phpcs` to confirm nothing is left.
  3. `phpstan` to analyse the code.
- After fixing PHPStan errors by hand, run `pint` and `phpcs` again on the changed files.
- Fix the code itself. Do not silence errors by adding `@phpstan-ignore` or `phpcs:ignore` comments, adding entries to the PHPStan baseline, lowering the PHPStan level or changing the PHPCS or Pint configuration, unless explicitly asked.

### Laravel Pint

The application's `pint.json` configures the R2H preset. If the application has no `pint.json`, add `--config vendor/r2h/r2h-coding-standard/pint.json` to the commands below.

Format a specific file:

```bash
./vendor/bin/pint app/Models/Post.php
```

Format multiple files or a directory:

```bash
./vendor/bin/pint app/Models/Post.php app/Http/Controllers/PostController.php
./vendor/bin/pint app/Models
```

Format all files with uncommitted changes:

```bash
./vendor/bin/pint --dirty
```

Only check the formatting without changing files:

```bash
./vendor/bin/pint --test app/Models/Post.php
```

### PHPCS

The application's `phpcs.xml` (or `phpcs.xml.dist`) configures the R2H standard. If the application has no such file, add `--standard=R2H` to the commands below.

Check a specific file:

```bash
./vendor/bin/phpcs app/Models/Post.php
```

Check multiple files at once:

```bash
./vendor/bin/phpcs app/Models/Post.php app/Http/Controllers/PostController.php
```

Check a specific directory:

```bash
./vendor/bin/phpcs app/Models
```

Automatically fix violations in a file or directory:

```bash
./vendor/bin/phpcbf app/Models/Post.php
./vendor/bin/phpcbf app/Models
```

### PHPStan

The application's `phpstan.neon` (or `phpstan.neon.dist`) configures PHPStan (usually with Larastan) and its level. Always use that configuration; do not pass a different `--level`.

Analyse a specific file:

```bash
./vendor/bin/phpstan analyse app/Models/Post.php
```

Analyse multiple files or a directory:

```bash
./vendor/bin/phpstan analyse app/Models/Post.php app/Http/Controllers/PostController.php
./vendor/bin/phpstan analyse app/Models
```

If PHPStan runs out of memory, raise the memory limit:

```bash
./vendor/bin/phpstan analyse --memory-limit=1G app/Models
```

### Example Workflow

After creating `app/Models/Post.php` and changing `app/Http/Controllers/PostController.php`:

```bash
./vendor/bin/pint app/Models/Post.php app/Http/Controllers/PostController.php
./vendor/bin/phpcbf app/Models/Post.php app/Http/Controllers/PostController.php
./vendor/bin/phpcs app/Models/Post.php app/Http/Controllers/PostController.php
./vendor/bin/phpstan analyse app/Models/Post.php app/Http/Controllers/PostController.php
```

Fix any remaining errors, then run the commands again until all three tools report no errors.
