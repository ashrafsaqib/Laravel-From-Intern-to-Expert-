# Hardcoding Values

Hardcoding values such as API keys, service credentials, or other configuration settings directly in your code is a common bad practice that can lead to several problems:

-   **Security Risks**: If your code is public (e.g., in a public Git repository), you are exposing sensitive credentials to the world.
-   **Poor Portability**: Your application's configuration is tied to the code. If you want to deploy your application to a different environment (e.g., from development to production), you have to change the code.
-   **Difficult to Manage**: As your application grows, it becomes harder to manage all the different configuration values scattered throughout your codebase.

Laravel provides a simple and secure way to manage your application's configuration using `.env` files and the `config()` helper function.

## Bad Code Example

Here's an example of a service class that uses a hardcoded API key.

```php
// app/Services/WeatherService.php

namespace App\Services;

use Illuminate\Support\Facades\Http;

class WeatherService
{
    protected $apiKey = 'super_secret_api_key'; // Hardcoded API key!

    public function getWeatherData($city)
    {
        $response = Http::get("https://api.weatherapi.com/v1/current.json?key={$this->apiKey}&q={$city}");

        return $response->json();
    }
}
```

This is a security risk and makes it difficult to change the API key for different environments.

## Good Code Example

Here's how you can refactor the code to use Laravel's configuration system.

### 1. Add the Value to your `.env` file

The `.env` file is used to store environment-specific variables. This file should not be committed to source control.

```dotenv
// .env

WEATHER_API_KEY=your_actual_api_key_for_local_dev
```

### 2. Create a Configuration File

Next, create a dedicated configuration file to retrieve the value from the `.env` file.

```php
// config/services.php

return [

    // ... other services

    'weather' => [
        'key' => env('WEATHER_API_KEY'),
    ],

];
```

### 3. Use the `config()` Helper in Your Code

Now you can use the `config()` helper function to access the configuration value in your code.

```php
// app/Services/WeatherService.php

namespace App\Services;

use Illuminate\Support\Facades\Http;

class WeatherService
{
    protected $apiKey;

    public function __construct()
    {
        $this->apiKey = config('services.weather.key');
    }

    public function getWeatherData($city)
    {
        $response = Http::get("https://api.weatherapi.com/v1/current.json?key={$this->apiKey}&q={$city}");

        return $response->json();
    }
}
```

By using this approach, you have:
-   Kept your sensitive API key out of your source code.
-   Made it easy to use different API keys for different environments (development, staging, production) by simply changing the value in the `.env` file for each environment.
-   Centralized your application's configuration, making it easier to manage.

**Important**: Remember to run `php artisan config:cache` in your production environment to cache your configuration files and improve performance. When you cache your configuration, the `env()` function will no longer be loaded from the `.env` file, so you must always access your configuration values through the `config()` helper.
