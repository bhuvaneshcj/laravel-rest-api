# Laravel 10 Rest API

This repository provides an example of a Rest API implemented in Laravel 10.

## Getting Started

### 1. Create a new project

```
composer create-project laravel/laravel restapi
```

### 2. Navigate to project folder

```
cd restapi
```

### 3. Set your Database name, Username, and Password in the .env file

This folder will be available in your project root folder

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE= // set database name
DB_USERNAME= // set username
DB_PASSWORD= // set password
```

### 4. Create Student Migration and Model
```
php artisan make:model Student -m
```

database/migrations/your_students_table
```
Schema::create('students', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('phone');
    $table->timestamps();
});
```

app/Models/Student.php
```
 protected $fillable = [
    'name',
    'phone',
];
```

### 5. Create Student Controller
```
php artisan make:controller Api/StudentController --model=Student
```

app/Http/Controllers/Api/StudentController.php
```
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Student;
use Illuminate\Http\Request;
use Validator;

class StudentController extends Controller
{
    /**
     * Display a listing of the resource.
     */
    public function index()
    {
        try {
            $students = Student::all();
            return response()->json($students, 200);
        } catch (\Exception $e) {
            return response()->json($e->getMessage(), 500);
        }
    }

    /**
     * Store a newly created resource in storage.
     */
    public function store(Request $request)
    {
        try {
            $validator = Validator::make($request->all(), [
                'name' => 'required|string|max:255',
                'phone' => 'required|string|max:255',
            ]);

            if ($validator->fails()) {
                return response()->json($validator->errors(), 400);
            }

            $student = Student::create([
                'name' => $request->name,
                'phone' => $request->phone,
            ]);
            return response()->json($student, 201);
        } catch (\Exception $e) {
            return response()->json($e->getMessage(), 500);
        }
    }

    /**
     * Display the specified resource.
     */
    public function show(Student $student)
    {
        try {
            return response()->json($student, 200);
        } catch (\Exception $e) {
            return response()->json($e->getMessage(), 500);
        }
    }

    /**
     * Update the specified resource in storage.
     */
    public function update(Request $request, Student $student)
    {
        try {
            $validator = Validator::make($request->all(), [
                'name' => 'required|string|max:255',
                'phone' => 'required|string|max:255',
            ]);

            if ($validator->fails()) {
                return response()->json($validator->errors(), 400);
            }

            $student->update([
                'name' => $request->name,
                'phone' => $request->phone,
            ]);
            return response()->json($student, 200);
        } catch (\Exception $e) {
            return response()->json($e->getMessage(), 500);
        }
    }

    /**
     * Remove the specified resource from storage.
     */
    public function destroy(Student $student)
    {
        try {
            $student->delete();
            return response()->json([], 200);
        } catch (\Exception $e) {
            return response()->json($e->getMessage(), 500);
        }
    }
}
```

### 6. Add students route
routes/api.php

```
<?php

use App\Http\Controllers\Api\StudentController;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "api" middleware group. Make something great!
|
 */

Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});

Route::resource('students', StudentController::class)->except('create', 'edit');
```

### 7. Migrate table
```
php artisan migrate
```

### 8. Run Project
```
php artisan serve
```
