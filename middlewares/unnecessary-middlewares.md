# Creating a New Middleware for Every Small Check

Middleware provides a convenient mechanism for filtering HTTP requests entering your application. For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to the login screen.

While middlewares are very powerful, creating a new middleware for every small check can lead to a bloated `app/Http/Middleware` directory. It's important to consider whether a middleware is the right tool for the job, or if another Laravel feature would be more appropriate.

## Bad Code Example

Let's say you have an application where only administrators can create new posts. You might be tempted to create a middleware to check if the user is an administrator.

```php
// app/Http/Middleware/CheckIsAdmin.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckIsAdmin
{
    public function handle(Request $request, Closure $next)
    {
        if (! $request->user()->is_admin) {
            abort(403, 'Unauthorized action.');
        }

        return $next($request);
    }
}
```

Then you would register this middleware and apply it to the `posts.create` and `posts.store` routes.

While this works, it's not the most elegant solution. If you have many different roles and permissions in your application, your `app/Http/Middleware` directory will quickly become cluttered with many small middleware classes.

## Good Code Example

A better approach is to use Laravel's built-in authorization features, such as Form Requests or Policies.

### Using Form Request Authorization

You can add authorization logic to your Form Request by overriding the `authorize` method.

```php
// app/Http/Requests/StorePostRequest.php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return $this->user()->is_admin;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'title' => 'required|max:255',
            'body' => 'required',
        ];
    }
}
```

Now, when you type-hint the `StorePostRequest` in your controller method, Laravel will automatically check if the user is authorized before validating the request. If the `authorize` method returns `false`, a `403 Forbidden` response will be returned.

### Using Policies

For more complex scenarios, you can use Policies. Policies are classes that organize authorization logic around a particular model or resource.

First, you would create a `PostPolicy`:
`php artisan make:policy PostPolicy --model=Post`

Then, you would define the `create` ability in the policy:

```php
// app/Policies/PostPolicy.php

namespace App\Policies;

use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class PostPolicy
{
    use HandlesAuthorization;

    /**
     * Determine whether the user can create models.
     *
     * @param  \App\Models\User  $user
     * @return mixed
     */
    public function create(User $user)
    {
        return $user->is_admin;
    }
}
```

Finally, you can use the `can` middleware in your routes file, or the `can` method in your controller.

```php
// routes/web.php

Route::post('/posts', [PostController::class, 'store'])->middleware('can:create,App\Models\Post');
```

```php
// app/Http/Controllers/PostController.php

public function store(Request $request)
{
    $this->authorize('create', Post::class);

    // ...
}
```

By using Form Requests or Policies, you can keep your authorization logic organized and avoid cluttering your application with unnecessary middleware.
