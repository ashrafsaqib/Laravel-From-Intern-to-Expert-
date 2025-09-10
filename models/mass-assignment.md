# Mass Assignment Vulnerability

Mass assignment is a feature in Laravel that allows you to create or update a model with an array of data. It's a convenient way to handle form submissions, but it can also be a security risk if not handled properly.

A mass assignment vulnerability occurs when a user can update any column in your database table, even if you didn't intend for them to. For example, a user could add an `is_admin` field to a form submission and give themselves administrative privileges.

To prevent this, Laravel provides two properties that you can add to your Eloquent models: `$fillable` and `$guarded`.

-   `$fillable`: An array of attributes that are allowed to be mass-assigned.
-   `$guarded`: An array of attributes that are not allowed to be mass-assigned.

You should use either `$fillable` or `$guarded`, but not both.

## Bad Code Example

Here's an example of a model without any mass assignment protection.

```php
// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasFactory;

    // No $fillable or $guarded property
}
```

With this model, a malicious user could potentially update any column in the `users` table.

```php
// app/Http/Controllers/UserController.php

public function update(Request $request, User $user)
{
    // If the user adds 'is_admin' => 1 to the request,
    // it will be updated in the database.
    $user->update($request->all());

    return redirect()->route('users.show', $user);
}
```

## Good Code Example

To protect against mass assignment, you should define the `$fillable` or `$guarded` property in your model.

### Using `$fillable`

The `$fillable` property is a "whitelist" of attributes that are allowed to be mass-assigned.

```php
// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasFactory;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name',
        'email',
        'password',
    ];
}
```

Now, only the `name`, `email`, and `password` attributes can be mass-assigned. If a user tries to pass an `is_admin` attribute in the request, it will be ignored.

### Using `$guarded`

The `$guarded` property is a "blacklist" of attributes that are not allowed to be mass-assigned.

```php
// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasFactory;

    /**
     * The attributes that aren't mass assignable.
     *
     * @var array
     */
    protected $guarded = [
        'is_admin',
    ];
}
```

In this case, all attributes can be mass-assigned except for `is_admin`.

It is generally recommended to use the `$fillable` property, as it is more explicit and less prone to errors. If you add a new column to your table and forget to add it to the `$guarded` array, it will be mass-assignable by default.
