# Not Using Collection Methods

When you retrieve multiple records from the database using Eloquent, you get an instance of the `Illuminate\Database\Eloquent\Collection` class. This class extends Laravel's base collection and provides a wide range of powerful methods for working with the data.

A common mistake for new developers is to treat the collection as a simple array and use native PHP loops (like `foreach`) to perform complex operations. While this works, it often leads to verbose and less readable code. By leveraging Laravel's collection methods, you can write more expressive, concise, and maintainable code.

## Bad Code Example

Here's an example where we get all active users, filter out the ones without a phone number, and then create a comma-separated list of their names.

```php
// app/Http/Controllers/UserController.php

namespace App\Http\Controllers;

use App\Models\User;

class UserController extends Controller
{
    public function getActiveUserNames()
    {
        $users = User::where('active', true)->get();

        $names = [];
        foreach ($users as $user) {
            if ($user->phone_number) {
                $names[] = $user->name;
            }
        }

        $userNamesString = implode(', ', $names);

        return $userNamesString;
    }
}
```

This code is verbose and requires several steps to achieve the desired result.

## Good Code Example

Here's how you can refactor the code to use collection methods.

```php
// app/Http/Controllers/UserController.php

namespace App\Http\Controllers;

use App\Models\User;

class UserController extends Controller
{
    public function getActiveUserNames()
    {
        $userNamesString = User::where('active', true)->get()
            ->filter(function ($user) {
                return $user->phone_number;
            })
            ->pluck('name')
            ->implode(', ');

        return $userNamesString;
    }
}
```

By chaining collection methods, we have made the code much more readable and expressive. Here's what each method does:
-   `filter()`: Filters the collection to only include users with a phone number.
-   `pluck()`: Retrieves all of the values for the `name` key.
-   `implode()`: Joins the array of names into a single string.

This is just one example. Laravel collections have dozens of other useful methods like `map()`, `reduce()`, `groupBy()`, `sum()`, `avg()`, and many more.

Before you reach for a `foreach` loop, always check the [Laravel documentation](https://laravel.com/docs/collections) to see if there's a collection method that can do the job more elegantly.
