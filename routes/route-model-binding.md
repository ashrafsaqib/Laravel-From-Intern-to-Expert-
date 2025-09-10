# Not Using Route Model Binding

Route model binding is a convenient feature in Laravel that allows you to automatically inject model instances into your routes or controller actions. Instead of manually fetching a model by its ID from the database, you can simply type-hint the model in your controller method, and Laravel will do the work for you.

By not using route model binding, you are writing more boilerplate code than necessary.

## Bad Code Example

Here's an example of a controller method that doesn't use route model binding. It manually finds the post by its ID.

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::get('/posts/{id}', [PostController::class, 'show']);
```

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;

class PostController extends Controller
{
    public function show($id)
    {
        $post = Post::findOrFail($id); // Manually find the post

        return view('posts.show', ['post' => $post]);
    }
}
```

In this example, we have to manually find the post using `Post::findOrFail($id)`. If the post is not found, a `ModelNotFoundException` will be thrown, which will result in a 404 page.

## Good Code Example

Here's how you can refactor the code to use route model binding.

```php
// routes/web.php

use App\Http\Controllers\PostController;

// The {post} parameter name should match the variable name in the controller method.
Route::get('/posts/{post}', [PostController::class, 'show']);
```

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;

class PostController extends Controller
{
    // Type-hint the Post model, and Laravel will automatically inject the instance.
    public function show(Post $post)
    {
        return view('posts.show', ['post' => $post]);
    }
}
```

By type-hinting the `Post` model in the `show` method, Laravel will automatically:
1.  Find the post in the database with the ID from the `{post}` route parameter.
2.  If the post is not found, it will automatically throw a `ModelNotFoundException` and generate a 404 response.
3.  If the post is found, it will inject the `Post` instance into the `$post` variable.

This makes your code cleaner, more readable, and less prone to errors.

### Customizing The Key

By default, route model binding will use the `id` column to find the model. If you want to use a different column, you can specify it in the route definition:

```php
Route::get('/posts/{post:slug}', [PostController::class, 'show']);
```

In this case, Laravel will use the `slug` column to find the post.

Alternatively, you can override the `getRouteKeyName` method in your Eloquent model:

```php
// app/Models/Post.php

public function getRouteKeyName()
{
    return 'slug';
}
```
