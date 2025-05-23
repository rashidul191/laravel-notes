## laravel-notes

1. Install Laravel

```bash
composer create-project lravel/laravel my-project "10.*"
```

2. Install Laravel Auth breeze

```bash
composer require laravel/breeze --dev
php artisan breeze:install
php artisan migrate
npm install && npm run dev
php artisan serve
```

## CK Editor Rich Editor Laravel

1. copy ck editor js cdn link and code

```bash
<script src="https://cdn.ckeditor.com/4.22.1/standard/ckeditor.js"></script>
<script>
        $('.ckEditor').each(function () {
            CKEDITOR.replace(this);
        });
        // Suppress specific console warning
        document.addEventListener('DOMContentLoaded', function() {
        if (typeof CKEDITOR !== 'undefined') {
            // Find the instance of CKEditor and set the configuration
            for (var instance in CKEDITOR.instances) {
                if (CKEDITOR.instances.hasOwnProperty(instance)) {
                    CKEDITOR.instances[instance].config.versionCheck = false;
                }
            }
        }
    });
</script>
```

2. Form Code of Laravel

```bash
   <form action="{{ route('form-data') }}" method="post">
        @csrf
        <textarea name="message1" class="ckEditor"></textarea>
        <textarea name="message2" class="ckEditor"></textarea>
        <button class="btn" type="submit">Submit</button>
   </form>
```
3. Blade Code

```bash
<p>{!! $data->message1 !!}</p>
<p>{!! $data->message2 !!}</p>
```
# Laravel Toastify

1. Installation

```bash
composer require redot/laravel-toastify
```

Then, add the following line to the head section of your `app.blade.php` file:

```bash
@toastifyCss
```

And the following line to the bottom of your `app.blade.php` file:

```bash
@toastifyJs
```

If you want to customize the default configuration, you can publish the configuration file using this command: (Optional)

```bash
php artisan vendor:publish --tag=toastify-config
```

## Usage

To display a toast message, simply call the toastify() helper function with the desired type and message:

`success`
`error`
`info`
`warning`

```bash
  <!-- Success Toast -->
    @if (session('success'))
    <script>
        toastify().success("{{ session('success') }}");
    </script>
    @endif

    <!-- Error Toast -->
    @if (session('error'))
    <script>
        toastify().success("{{ session('error') }}");
    </script>
    @endif
```

## Configuration

The configuration file for Laravel Toastify is located at `config/toastify.php`. Here you can specify the CDN links for the toastify library and customize the default toastifiers.

```bash
'toastifiers' => [
    'custom' => [
        'duration' => 5000,
        'style' => [
            'background' => '#2fb344',
            'color' => '#fff',
        ],
    ],
],
```

# Key Pair Value Store and Get form Database

1. Create Helper.php `app/Http/Helper.php` file.
````bash
<?php
use App\Models\BusinessSetting;
if (!function_exists("business_setting")) {
    function business_setting($key, $default = null)
    {
        return BusinessSetting::where('key', $key)->value('value') ?? $default;
    }
}
````
2. add code of `composer.json` file
````bash
 "autoload": {    
    #   "psr-4": {
    #         "App\\": "app/",
    #         "Database\\Factories\\": "database/factories/",
    #         "Database\\Seeders\\": "database/seeders/"
    #     },

    # Add code 
        "files": [
            "app/Http/Helpers.php"
        ]
    },
````

3. Run this command
After editing `composer.json`, need to update the Composer autoload files:
````bash
composer dump-autoload
````
4. Controller Code
````bash
   public function store(Request $request) {
       $data = $request->except('_token');
       $t = time();
        foreach ($data as $key => $value) {
            if ($request->hasFile($key)) {

                # $file = $request->file($key);
                # $filename = $key . '.' . $file->getClientOriginalExtension();
                # $path = $file->storeAs('settings', $filename, 'public');
                # $value = 'storage/' . $path;

                $file = $request->file($key);
                $filename = $key . '.' . $file->getClientOriginalExtension();
                $newFileName = "{$t}-{$filename}";
                $value = "uploads/{$newFileName}";
                $file->move(public_path('uploads'), $newFileName);
            }
            BusinessSetting::updateOrCreate(['key' => $key], ['value' => $value]);
          }
        return redirect()->back()->with('success', 'Settings updated successfully.');
    }
````

5. Laravel Blade Form Code :

````bash
<form action="{{ route('anyRouteHere.store') }}" method="POST" enctype="multipart/form-data">
            @csrf
            <input type="text" name="address" class="form-control"
                value="{{ business_setting('address') }}">

            <input type="text" name="phone" class="form-control"
                value="{{ business_setting('phone')}}">
            <input type="email" name="email" class="form-control"
                value="{{ business_setting('email')}}">
            <button type="submit" class="btn btn-primary btn-lg w-50">Save</button>
        </form>
````
Output Code :
````bash
  <h2>{{ business_setting('about_title') }}</h2>
````

## ImageUpload Helper Function

1. Helper Code
````bash
<?php
namespace App\Http\Helper;
use Illuminate\Support\Str;
class ImageUpload
{
    public static function fileUpload($file, $title = "with-out-title",  $path = "uploads")
    {
        if (!$file || !$file->isValid()) {
            return null;
        }       
        $slug = Str::slug($title);
        $timestamp = time();
        $originalFileName = $file->getClientOriginalName();        
        $newFileName = "{$slug}-{$timestamp}-{$originalFileName}";
        $file->move(public_path($path), $newFileName);
        return "{$path}/{$newFileName}";
    }
}
````

2. Helper Function Calling
````bash
use App\Http\Helper\ImageUpload;
 $image = ImageUpload::fileUpload($request->file('image'), $title); 
````