# Putting Logic in Views

Blade is a powerful templating engine, but its primary responsibility is to display data, not to perform business logic. A common mistake is to clutter your Blade files with complex PHP logic, such as querying the database, performing calculations, or making decisions based on the application's state.

When you put logic in your views, you are violating the separation of concerns principle, which makes your application harder to test, debug, and maintain.

## Bad Code Example

Here's an example of a Blade view that contains too much logic. It fetches the latest posts from the database and checks if the user is an administrator.

```blade
// resources/views/dashboard.blade.php

<h1>Dashboard</h1>

<h2>Latest Posts</h2>
<ul>
    @foreach (App\Models\Post::latest()->take(5)->get() as $post)
        <li>{{ $post->title }}</li>
    @endforeach
</ul>

@if (auth()->user()->is_admin)
    <a href="/admin">Go to Admin Panel</a>
@endif
```

This view is tightly coupled to the `Post` model and the authentication system. It's also difficult to test because you can't easily mock the database query or the authenticated user.

## Good Code Example

The logic should be moved out of the view and into the controller or a dedicated class like a view composer or presenter.

### 1. Prepare Data in the Controller

The controller should be responsible for fetching the data and passing it to the view.

```php
// app/Http/Controllers/DashboardController.php

namespace App\Http\Controllers;

use App\Models\Post;

class DashboardController extends Controller
{
    public function __invoke()
    {
        $latestPosts = Post::latest()->take(5)->get();

        return view('dashboard', [
            'latestPosts' => $latestPosts,
        ]);
    }
}
```

### 2. Use `@can` Directive for Authorization

For authorization checks, you should use Laravel's built-in authorization features and the `@can` Blade directive.

First, define a policy for the admin check (or use an existing one).

```php
// app/Policies/AdminPolicy.php (or a relevant policy)

public function viewPanel(User $user)
{
    return $user->is_admin;
}
```

Then, use the `@can` directive in your view.

```blade
// resources/views/dashboard.blade.php

<h1>Dashboard</h1>

<h2>Latest Posts</h2>
<ul>
    @foreach ($latestPosts as $post)
        <li>{{ $post->title }}</li>
    @endforeach
</ul>

@can('viewPanel', App\Models\User::class)
    <a href="/admin">Go to Admin Panel</a>
@endcan
```

By moving the logic out of the view, you have:
-   Made your view cleaner and more focused on presentation.
-   Decoupled your view from the database and authentication system.
-   Made your code easier to test and maintain.
-   Centralized your data-fetching and authorization logic.

For more complex scenarios where logic is needed across multiple views, consider using [View Composers](https://laravel.com/docs/views#view-composers).
