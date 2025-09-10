# Not Using Factories

When writing tests, you often need to create model instances to seed your database with test data. A common mistake is to manually create these model instances in your test methods using `Model::create()`. This approach is verbose, repetitive, and makes your tests harder to read and maintain.

Laravel's model factories provide a convenient and expressive way to define a "default" state for your models and generate test data.

## Bad Code Example

Here's an example of a test where we are manually creating a user and a post.

```php
// tests/Feature/PostTest.php

namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    public function test_a_user_can_view_a_post()
    {
        // Manually creating a user
        $user = User::create([
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => bcrypt('password'),
        ]);

        // Manually creating a post
        $post = Post::create([
            'user_id' => $user->id,
            'title' => 'My First Post',
            'body' => 'This is the body of my first post.',
        ]);

        $response = $this->get('/posts/' . $post->id);

        $response->assertStatus(200);
        $response->assertSee('My First Post');
    }
}
```

This test is verbose and tightly coupled to the structure of the `User` and `Post` models. If you add a new required field to the `users` table, this test will break, and you'll have to update it, along with every other test where you manually create a user.

## Good Code Example

Here's how you can refactor the code to use model factories.

### 1. Define Your Factories

First, you need to define factories for your models. You can create a new factory using the `make:factory` Artisan command.

```php
// database/factories/UserFactory.php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    public function definition()
    {
        return [
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }
}
```

```php
// database/factories/PostFactory.php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    public function definition()
    {
        return [
            'user_id' => User::factory(), // Automatically create a user for the post
            'title' => $this->faker->sentence(),
            'body' => $this->faker->paragraph(),
        ];
    }
}
```

### 2. Use the Factories in Your Tests

Now you can use these factories in your tests to generate model instances.

```php
// tests/Feature/PostTest.php

namespace Tests\Feature;

use App\Models\Post;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    public function test_a_user_can_view_a_post()
    {
        // Use the factory to create a post.
        // A user will be automatically created and associated with the post.
        $post = Post::factory()->create();

        $response = $this->get('/posts/' . $post->id);

        $response->assertStatus(200);
        $response->assertSee($post->title);
    }
}
```

The test is now much cleaner and more readable. The factory handles the details of creating the model instances. If you need to override some of the default values, you can pass an array to the `create` method:

`$post = Post::factory()->create(['title' => 'My Custom Title']);`

By using model factories, you can significantly reduce the amount of boilerplate code in your tests, making them easier to write and maintain.
