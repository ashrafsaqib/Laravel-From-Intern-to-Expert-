# Not Using Blade Components

When building a web application, you often find yourself reusing the same UI elements across multiple pages. Examples include buttons, form inputs, alerts, and modals. A common mistake is to copy and paste the HTML for these elements, which leads to code duplication and makes your application harder to maintain.

If you need to change the design of a button, you would have to find and update every single instance of that button in your codebase.

Blade components provide a powerful way to extract these reusable UI elements into their own dedicated classes and templates.

## Bad Code Example

Here's an example where we are defining the HTML for a primary button in multiple places.

```blade
// resources/views/users/create.blade.php

<button type="submit" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
    Create User
</button>
```

```blade
// resources/views/posts/create.blade.php

<button type="submit" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
    Create Post
</button>
```

If you want to change the color or padding of the primary button, you have to update it in both files.

## Good Code Example

Here's how you can refactor the code to use a Blade component.

### 1. Create the Component

You can create a new component using the following Artisan command:
`php artisan make:component PrimaryButton`

This will create a view file at `resources/views/components/primary-button.blade.php`.

### 2. Define the Component's Template

Now you can move the button HTML into the component's view file. You can use the `$slot` variable to inject content into the component.

```blade
// resources/views/components/primary-button.blade.php

<button {{ $attributes->merge(['type' => 'submit', 'class' => 'px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600']) }}>
    {{ $slot }}
</button>
```
The `$attributes->merge()` call is important as it allows you to pass additional HTML attributes (like `type`, `class`, `id`, etc.) to the component from where it's being used.

### 3. Use the Component in Your Views

Now you can use the component in your views using a custom HTML tag.

```blade
// resources/views/users/create.blade.php

<x-primary-button>
    Create User
</x-primary-button>
```

```blade
// resources/views/posts/create.blade.php

<x-primary-button>
    Create Post
</x-primary-button>
```

Now, if you need to change the design of the primary button, you only need to update the `resources/views/components/primary-button.blade.php` file, and the change will be reflected everywhere the component is used.

You can also pass data to your components using attributes:
`<x-alert type="error" message="Something went wrong." />`

By using Blade components, you can build a library of reusable UI elements, which will make your codebase more consistent, maintainable, and easier to scale.
