# Missing Accessors & Mutators

Accessors and mutators allow you to format Eloquent attribute values when you retrieve them from a model or set them on a model. Accessors are used to format attributes for display, while mutators are used to transform attributes before they are saved to the database.

A common mistake is to put this formatting and transformation logic directly in your controllers or views. This leads to code duplication and makes your code harder to maintain. By using accessors and mutators, you can define this logic in one place—your model—and ensure that your data is always consistently formatted.

## Bad Code Example

Here's an example where we are formatting a user's name to be capitalized and hashing a password directly in the controller.

```php
// app/Http/Controllers/UserController.php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;

class UserController extends Controller
{
    public function show(User $user)
    {
        $user->name = ucwords($user->name); // Formatting logic in the controller

        return view('users.show', compact('user'));
    }

    public function store(Request $request)
    {
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password), // Transformation logic in the controller
        ]);

        return redirect()->route('users.show', $user);
    }
}
```

If you need to display the user's capitalized name in another part of your application, you'll have to duplicate the `ucwords()` logic. Similarly, if you have another way to create a user (e.g., a console command), you'll have to remember to hash the password there as well.

## Good Code Example

Here's how you can refactor the code to use accessors and mutators.

### Using an Accessor

You can define an accessor by creating a `get{Attribute}Attribute` method on your model, where `{Attribute}` is the "studly" cased name of the column you wish to access.

```php
// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    // ...

    /**
     * Get the user's name.
     *
     * @return \Illuminate\Database\Eloquent\Casts\Attribute
     */
    protected function name(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucwords($value),
        );
    }
}
```

Now, whenever you access the `name` attribute on a `User` model, the `ucwords()` function will be automatically applied.

### Using a Mutator

You can define a mutator by creating a `set{Attribute}Attribute` method on your model. With the new syntax, you define it within the `Attribute::make()` method.

```php
// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Support\Facades\Hash;

class User extends Authenticatable
{
    // ...

    /**
     * Get the user's password.
     *
     * @return \Illuminate\Database\Eloquent\Casts\Attribute
     */
    protected function password(): Attribute
    {
        return Attribute::make(
            set: fn ($value) => Hash::make($value),
        );
    }
}
```

Now, whenever you set the `password` attribute on a `User` model, it will be automatically hashed before being saved to the database.

With accessors and mutators, your controller becomes much cleaner:

```php
// app/Http/Controllers/UserController.php

public function show(User $user)
{
    // The name is already capitalized thanks to the accessor.
    return view('users.show', compact('user'));
}

public function store(Request $request)
{
    // The password will be automatically hashed by the mutator.
    $user = User::create($request->all());

    return redirect()->route('users.show', $user);
}
```

By using accessors and mutators, you can keep your formatting and transformation logic centralized in your models, leading to cleaner, more maintainable code.
