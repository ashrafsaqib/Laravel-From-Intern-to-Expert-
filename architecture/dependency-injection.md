# Not Using Dependency Injection

Dependency Injection (DI) is a design pattern where the dependencies of a class (i.e., the other objects it needs to do its job) are "injected" into it, rather than being created by the class itself. Laravel's Service Container is a powerful tool for managing class dependencies and performing dependency injection.

A common mistake for developers new to this concept is to manually create instances of their dependencies inside their classes. This leads to tightly coupled code, which is difficult to test and maintain.

## Bad Code Example

Here's an example of a controller that manually creates an instance of a `WeatherService`.

```php
// app/Http/Controllers/WeatherController.php

namespace App\Http\Controllers;

use App\Services\WeatherService;

class WeatherController extends Controller
{
    public function show($city)
    {
        $weatherService = new WeatherService(); // Manually creating the dependency
        $weatherData = $weatherService->getWeatherData($city);

        return view('weather.show', compact('weatherData'));
    }
}
```

This controller is tightly coupled to the `WeatherService` class. If the `WeatherService`'s constructor changes (e.g., it needs an API key), you would have to update every place where it's instantiated. More importantly, it's very difficult to test this controller because you can't easily replace the `WeatherService` with a fake or mock version during testing.

## Good Code Example

Here's how you can refactor the code to use dependency injection.

### 1. Type-hint the Dependency

You can "inject" the dependency by type-hinting it in the constructor or a method of the class. Laravel's service container will automatically resolve and inject the dependency for you.

```php
// app/Http/Controllers/WeatherController.php

namespace App\Http\Controllers;

use App\Services\WeatherService;

class WeatherController extends Controller
{
    protected $weatherService;

    // Inject the service via the constructor
    public function __construct(WeatherService $weatherService)
    {
        $this->weatherService = $weatherService;
    }

    public function show($city)
    {
        $weatherData = $this->weatherService->getWeatherData($city);

        return view('weather.show', compact('weatherData'));
    }
}
```

Laravel's service container automatically knows how to create the `WeatherService` instance. If `WeatherService` itself has dependencies, the container will automatically resolve those as well, recursively.

### Benefits of Dependency Injection

By using dependency injection, you have:
-   **Decoupled your code**: The `WeatherController` is no longer responsible for creating the `WeatherService`. It just needs *any* object that conforms to the `WeatherService` "contract".
-   **Made your code easier to test**: During testing, you can easily swap out the real `WeatherService` with a mock implementation.

```php
// tests/Feature/WeatherControllerTest.php

public function test_it_shows_weather_data()
{
    // Create a mock of the WeatherService
    $mock = $this->mock(WeatherService::class);

    // Define what the mock should return when a method is called
    $mock->shouldReceive('getWeatherData')->with('London')->andReturn(['temperature' => '15C']);

    // The service container will automatically inject our mock into the controller
    $response = $this->get('/weather/London');

    $response->assertStatus(200);
    $response->assertSee('15C');
}
```

This makes your tests faster and more reliable, as they are not dependent on an external service. Understanding and using Laravel's service container and dependency injection is fundamental to building modern, testable, and maintainable applications.
