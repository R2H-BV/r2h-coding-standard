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
