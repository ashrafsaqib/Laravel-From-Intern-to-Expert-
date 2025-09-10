# Using `@include` in a Loop

When you need to render a sub-view for each item in a collection, you might be tempted to use a `foreach` loop and the `@include` directive. While this works, Laravel provides a more elegant and performant way to achieve the same result: the `@each` directive.

Using `@include` inside a loop can be less efficient because it has to load and parse the same view file on every iteration. The `@each` directive, on the other hand, is optimized for this specific use case.

## Bad Code Example

Here's an example of using `@include` inside a `foreach` loop to render a list of posts.

```blade
// resources/views/posts/index.blade.php

@foreach ($posts as $post)
    @include('posts.partials.post', ['post' => $post])
@endforeach
```

```blade
// resources/views/posts/partials/post.blade.php

<div>
    <h2>{{ $post->title }}</h2>
    <p>{{ $post->excerpt }}</p>
</div>
```

This code is perfectly functional, but it's not the most efficient or concise way to do it.

## Good Code Example

Here's how you can refactor the code to use the `@each` directive.

The `@each` directive takes three arguments:
1.  The view to render for each element.
2.  The collection or array to iterate over.
3.  The variable name to use for the item within the view.

```blade
// resources/views/posts/index.blade.php

@each('posts.partials.post', $posts, 'post')
```

This single line of code does the exact same thing as the `foreach` loop with `@include`. It's more concise, easier to read, and can be more performant, especially for large collections.

### Optional Fourth Argument

The `@each` directive also accepts a fourth argument, which is the view to render if the collection is empty.

```blade
@each('posts.partials.post', $posts, 'post', 'posts.partials.empty')
```

```blade
// resources/views/posts/partials/empty.blade.php

<p>No posts found.</p>
```

This is a convenient way to handle the "empty state" of a list without adding an extra `@if` check in your main view.

By using `@each` instead of `@include` in a loop, you are writing cleaner, more expressive, and potentially more performant code. It's a small change that can improve the quality of your Blade templates.
