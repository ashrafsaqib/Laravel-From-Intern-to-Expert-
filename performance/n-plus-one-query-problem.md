# N+1 Query Problem

The N+1 query problem is a common performance issue that occurs when an application makes one query to retrieve a list of items, and then makes one additional query for each of those items to retrieve related data.

For example, if you have 100 books and you want to display the author for each book, you might end up running 101 queries: one to get the 100 books, and 100 more to get the author for each book. This can quickly become a bottleneck for your application.

## Bad Code Example

Here's an example of code that causes the N+1 query problem. Let's say we have a `Book` model and an `Author` model, with a one-to-many relationship between them.

```php
// app/Http/Controllers/BookController.php

namespace App\Http\Controllers;

use App\Models\Book;

class BookController extends Controller
{
    public function index()
    {
        $books = Book::all(); // 1 query to get all books

        return view('books.index', compact('books'));
    }
}
```

```blade
// resources/views/books/index.blade.php

@foreach ($books as $book)
    <p>{{ $book->title }} by {{ $book->author->name }}</p> // N queries to get the author for each book
@endforeach
```

In this example, the code first retrieves all books with a single query. Then, inside the loop, it accesses the `author` relationship for each book, which triggers a separate query to fetch the author's data.

## Good Code Example

To solve the N+1 query problem, you can use **eager loading**. Eager loading allows you to retrieve all the related data in a single query. In Laravel, you can use the `with()` method to specify the relationships you want to eager load.

```php
// app/Http/Controllers/BookController.php

namespace App\Http\Controllers;

use App\Models\Book;

class BookController extends Controller
{
    public function index()
    {
        $books = Book::with('author')->get(); // 2 queries total, regardless of the number of books

        return view('books.index', compact('books'));
    }
}
```

```blade
// resources/views/books/index.blade.php

@foreach ($books as $book)
    <p>{{ $book->title }} by {{ $book->author->name }}</p> // No additional queries
@endforeach
```

By using `Book::with('author')->get()`, you're telling Eloquent to fetch all the books and their authors in just two queries: one for the books, and one for all the authors related to those books. This significantly reduces the number of queries and improves the performance of your application.
