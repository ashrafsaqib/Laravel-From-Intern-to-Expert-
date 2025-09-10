# Fat Controllers

A "fat controller" is a controller that contains too much logic. This is a common anti-pattern in MVC frameworks like Laravel. When controllers become too large, they become difficult to read, test, and maintain.

The logic in a controller should be limited to:
-   Receiving the request.
-   Calling the necessary services or models to handle the business logic.
-   Returning a response (e.g., a view, a redirect, or a JSON response).

Any other logic, such as business logic, data validation, or complex query building, should be moved to other classes, such as:
-   **Service classes**: To handle business logic.
-   **Action classes**: For single-action controllers.
-   **Form Requests**: To handle validation.
-   **Query Builders/Scopes**: To handle complex queries.

## Bad Code Example

Here's an example of a fat controller that handles user registration. It includes validation, user creation, and sending a welcome email, all within the `store` method.

```php
// app/Http/Controllers/UserController.php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Mail;

class UserController extends Controller
{
    public function store(Request $request)
    {
        // 1. Validation
        $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ]);

        // 2. User Creation
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        // 3. Send Welcome Email
        Mail::to($user->email)->send(new WelcomeEmail($user));

        // 4. Login the user
        auth()->login($user);

        return redirect('/dashboard');
    }
}
```

## Good Code Example

Here's how you can refactor the fat controller into a leaner one by moving the logic to other classes.

### 1. Use a Form Request for Validation

Create a `RegisterUserRequest` to handle the validation logic.

```php
// app/Http/Requests/RegisterUserRequest.php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class RegisterUserRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ];
    }
}
```

### 2. Use a Service Class for Business Logic

Create a `UserService` to handle the user creation and sending the welcome email.

```php
// app/Services/UserService.php

namespace App\Services;

use App\Models\User;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Mail;
use App\Mail\WelcomeEmail;

class UserService
{
    public function createUser(array $data): User
    {
        $user = User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
        ]);

        Mail::to($user->email)->send(new WelcomeEmail($user));

        return $user;
    }
}
```

### 3. The Lean Controller

Now the controller is much cleaner and easier to read.

```php
// app/Http/Controllers/UserController.php

namespace App\Http\Controllers;

use App\Http\Requests\RegisterUserRequest;
use App\Services\UserService;

class UserController extends Controller
{
    protected $userService;

    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    public function store(RegisterUserRequest $request)
    {
        $user = $this->userService->createUser($request->validated());

        auth()->login($user);

        return redirect('/dashboard');
    }
}
```

By refactoring the fat controller, you've made your code more organized, reusable, and easier to test.
