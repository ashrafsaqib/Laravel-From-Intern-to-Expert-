# Not Using Eloquent Scopes

Eloquent scopes allow you to define common sets of query constraints that you can easily reuse throughout your application. When you find yourself writing the same query constraints in multiple places, you should consider extracting them into a scope.

By not using scopes, you are duplicating code, making your application harder to maintain. If you need to change the logic for a common query, you'll have to find and update every place where it's used.

## Bad Code Example

Here's an example of a controller where we are fetching published posts and posts by a specific user in multiple methods. The query logic is duplicated.

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        // Get all published posts
        $posts = Post::where('published', true)->latest()->get();

        return view('posts.index', compact('posts'));
    }

    public function show(Post $post)
    {
        // The post is already fetched via route model binding, but for the sake of example,
        // let's say we need to get other published posts by the same author.
        $relatedPosts = Post::where('published', true)
                              ->where('user_id', $post->user_id)
                              ->where('id', '!=', $post->id)
                              ->get();

        return view('posts.show', compact('post', 'relatedPosts'));
    }
}
```

## Good Code Example

Here's how you can refactor the code to use local scopes.

### 1. Define the Scopes on the Model

You can define scopes in your Eloquent model by creating a public method prefixed with `scope`.

```php
// app/Models/Post.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class Post extends Model
{
    /**
     * Scope a query to only include published posts.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $query
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopePublished(Builder $query)
    {
        return $query->where('published', true);
    }

    /**
     * Scope a query to only include posts by a specific user.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $query
     * @param  int  $userId
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopeByUser(Builder $query, $userId)
    {
        return $query->where('user_id', $userId);
    }
}
```

### 2. Use the Scopes in the Controller

Now you can use these scopes in your controller. Notice how much cleaner and more readable the code becomes.

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::published()->latest()->get();

        return view('posts.index', compact('posts'));
    }

    public function show(Post $post)
    {
        $relatedPosts = Post::published()
                              ->byUser($post->user_id)
                              ->where('id', '!=', $post->id)
                              ->get();

        return view('posts.show', compact('post', 'relatedPosts'));
    }
}
```

By using scopes, you have:
-   Made your code more readable and expressive.
-   Avoided code duplication.
-   Centralized your query logic in the model, making it easier to maintain.
-   You can chain scopes together, just like any other Eloquent method.
