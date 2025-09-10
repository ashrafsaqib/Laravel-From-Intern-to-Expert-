# Not Using Route Groups

As your application grows, your `routes/web.php` file can become very long and repetitive. You might find yourself applying the same middleware, prefix, or other attributes to a large number of routes.

Route groups allow you to group a set of routes and apply attributes to all of them at once. This helps to keep your routes file clean, organized, and easier to read.

## Bad Code Example

Here's an example of a routes file without route groups. Notice the repetition of the `admin` prefix and the `auth` middleware.

```php
// routes/web.php

use App\Http\Controllers\Admin\DashboardController;
use App\Http\Controllers\Admin\PostController;
use App\Http\Controllers\Admin\UserController;

Route::get('/admin/dashboard', [DashboardController::class, 'index'])->middleware('auth');

Route::get('/admin/users', [UserController::class, 'index'])->middleware('auth');
Route::get('/admin/users/create', [UserController::class, 'create'])->middleware('auth');
Route::post('/admin/users', [UserController::class, 'store'])->middleware('auth');
Route::get('/admin/users/{user}/edit', [UserController::class, 'edit'])->middleware('auth');
Route::put('/admin/users/{user}', [UserController::class, 'update'])->middleware('auth');
Route::delete('/admin/users/{user}', [UserController::class, 'destroy'])->middleware('auth');

Route::get('/admin/posts', [PostController::class, 'index'])->middleware('auth');
Route::get('/admin/posts/create', [PostController::class, 'create'])->middleware('auth');
Route::post('/admin/posts', [PostController::class, 'store'])->middleware('auth');
// ... and so on
```

This code is verbose and error-prone. If you need to change the prefix or add a new middleware, you have to update every single route.

## Good Code Example

Here's how you can refactor the code to use route groups.

```php
// routes/web.php

use App\Http\Controllers\Admin\DashboardController;
use App\Http\Controllers\Admin\PostController;
use App\Http\Controllers\Admin\UserController;

Route::middleware(['auth'])->prefix('admin')->name('admin.')->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index'])->name('dashboard');

    Route::resource('users', UserController::class);
    Route::resource('posts', PostController::class);
});
```

By using a route group, we have:
-   Applied the `auth` middleware to all the routes within the group.
-   Prefixed the URI of all the routes with `admin`.
-   Prefixed the name of all the routes with `admin.`.
-   Made the code much cleaner and easier to read.
-   Used `Route::resource` to define the common "CRUD" routes for users and posts, which further reduces the amount of code.

Now, if you need to change the prefix or add a new middleware, you only need to change it in one place.
