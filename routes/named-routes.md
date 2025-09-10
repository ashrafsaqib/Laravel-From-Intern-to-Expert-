# Using URLs instead of Named Routes

Named routes allow you to generate URLs for specific routes. This is a powerful feature that can make your application more robust and easier to maintain.

When you hardcode URLs in your application, you are creating a tight coupling between your code and your route definitions. If you ever need to change a URL, you will have to search your entire codebase and update every occurrence of that URL. This is time-consuming and error-prone.

By using named routes, you can refer to your routes by name. If you need to change a URL, you only need to update it in your `routes/web.php` file, and all the calls to the `route()` helper function will automatically generate the correct URL.

## Bad Code Example

Here's an example of code that uses hardcoded URLs.

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::get('/blog/posts/{post}', [PostController::class, 'show']);
Route::get('/blog/posts', [PostController::class, 'index']);
```

```php
// resources/views/posts/index.blade.php

<a href="/blog/posts/{{ $post->id }}">View Post</a>
```

```php
// app/Http/Controllers/PostController.php

public function store(Request $request)
{
    // ...
    return redirect('/blog/posts');
}
```

If you ever decide to change the URL structure from `/blog/posts` to `/articles`, you will have to update the URL in the view, the controller, and any other place where it's used.

## Good Code Example

Here's how you can refactor the code to use named routes.

### 1. Name your routes

First, you need to give your routes a name using the `name()` method.

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::get('/blog/posts/{post}', [PostController::class, 'show'])->name('posts.show');
Route::get('/blog/posts', [PostController::class, 'index'])->name('posts.index');
```

It's a good convention to name your routes using a `resource.action` syntax (e.g., `posts.index`, `posts.show`).

### 2. Use the `route()` helper function

Now you can use the `route()` helper function to generate URLs.

```php
// resources/views/posts/index.blade.php

<a href="{{ route('posts.show', ['post' => $post->id]) }}">View Post</a>
```

```php
// app/Http/Controllers/PostController.php

public function store(Request $request)
{
    // ...
    return redirect()->route('posts.index');
}
```

Now, if you need to change the URL, you only need to update it in your `routes/web.php` file. The `route()` helper function will automatically generate the correct URL, and you won't have to change your views or controllers. This makes your application much more maintainable.
