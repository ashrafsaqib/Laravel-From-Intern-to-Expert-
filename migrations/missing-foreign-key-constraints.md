# Missing Foreign Key Constraints

Foreign key constraints are a crucial part of database design. They enforce referential integrity between tables, which means they prevent you from creating orphaned records.

For example, if you have a `posts` table and a `users` table, a foreign key constraint can ensure that every post in the `posts` table is associated with a valid user in the `users` table.

By not using foreign key constraints, you are risking data inconsistency in your database.

## Bad Code Example

Here's an example of a migration that creates a `user_id` column in the `posts` table but doesn't add a foreign key constraint.

```php
// database/migrations/xxxx_xx_xx_create_posts_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->unsignedBigInteger('user_id'); // Foreign key column
            $table->timestamps();
        });
    }

    // ...
}
```

With this migration, it's possible to create a post with a `user_id` that doesn't exist in the `users` table. It's also possible to delete a user from the `users` table, leaving their posts as orphaned records in the `posts` table.

## Good Code Example

Here's how you can add a foreign key constraint to the `user_id` column. Laravel provides a convenient way to do this using the `foreignId()` and `constrained()` methods.

```php
// database/migrations/xxxx_xx_xx_create_posts_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->foreignId('user_id')->constrained(); // Foreign key constraint
            $table->timestamps();
        });
    }

    // ...
}
```

By using `foreignId('user_id')->constrained()`, Laravel will automatically:
-   Create an `unsignedBigInteger` column named `user_id`.
-   Add a foreign key constraint that references the `id` column on the `users` table.

### onDelete and onUpdate

You can also specify the `onDelete` and `onUpdate` actions for the foreign key constraint. This determines what happens to the posts when the associated user is deleted or updated.

```php
$table->foreignId('user_id')
      ->constrained()
      ->onDelete('cascade'); // or 'restrict', 'set null', etc.
```

-   `onDelete('cascade')`: If a user is deleted, all of their posts will be deleted as well. This is useful for maintaining data integrity.
-   `onDelete('restrict')`: Prevents a user from being deleted if they have any posts. This is the default behavior.
-   `onDelete('set null')`: If a user is deleted, the `user_id` of their posts will be set to `null`. This requires the `user_id` column to be nullable.

By using foreign key constraints, you can ensure that your database remains in a consistent state.
