# Validation in Controllers

Placing validation logic directly within controller methods is a common practice for developers new to Laravel. While it works, it can lead to several issues:

-   **Fat Controllers**: As mentioned in the "Fat Controllers" topic, this adds unnecessary bulk to your controllers.
-   **Code Duplication**: If the same validation logic is needed in multiple controller methods (e.g., for `store` and `update`), you'll have to duplicate the validation rules.
-   **Reduced Reusability**: The validation logic is tied to the controller and cannot be easily reused in other parts of your application (e.g., in an API endpoint or a console command).

Laravel provides a powerful feature called **Form Requests** to handle validation in a more organized and reusable way.

## Bad Code Example

Here's an example of validation logic placed directly in a controller method.

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function store(Request $request)
    {
        $request->validate([
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
            'publish_at' => 'nullable|date',
        ]);

        $post = Post::create($request->all());

        return redirect()->route('posts.show', $post);
    }

    public function update(Request $request, Post $post)
    {
        $request->validate([
            'title' => 'required|max:255|unique:posts,title,' . $post->id,
            'body' => 'required',
            'publish_at' => 'nullable|date',
        ]);

        $post->update($request->all());

        return redirect()->route('posts.show', $post);
    }
}
```

Notice the duplication of validation rules in the `store` and `update` methods. The `unique` rule is also more complex in the `update` method.

## Good Code Example

Here's how you can refactor the validation logic into a Form Request.

### 1. Create a Form Request

You can create a new form request class using the following Artisan command:
`php artisan make:request StorePostRequest`

This will create a new file at `app/Http/Requests/StorePostRequest.php`.

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
        // You can add authorization logic here, for example:
        // return $this->user()->can('create', Post::class);
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        $postId = $this->route('post') ? $this->route('post')->id : null;

        return [
            'title' => 'required|max:255|unique:posts,title,' . $postId,
            'body' => 'required',
            'publish_at' => 'nullable|date',
        ];
    }
}
```

### 2. Use the Form Request in the Controller

Now you can type-hint the `StorePostRequest` in your controller methods. Laravel will automatically validate the request before the controller method is called.

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;
use App\Http\Requests\StorePostRequest;

class PostController extends Controller
{
    public function store(StorePostRequest $request)
    {
        $post = Post::create($request->validated());

        return redirect()->route('posts.show', $post);
    }

    public function update(StorePostRequest $request, Post $post)
    {
        $post->update($request->validated());

        return redirect()->route('posts.show', $post);
    }
}
```

By using Form Requests, you have:
-   Removed the validation logic from the controller.
-   Avoided code duplication.
-   Made the validation logic reusable.
-   The controller is now much cleaner and more focused on its core responsibility.
-   You can get the validated data by calling `$request->validated()`.
