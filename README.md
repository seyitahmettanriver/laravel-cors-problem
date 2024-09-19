

# CORS and `OPTIONS` Requests Issue

I spent two days troubleshooting an issue related to CORS and `OPTIONS` requests. The problem occurs when sending headers data with AJAX or fetch requests. The browser immediately sends an `OPTIONS` request to the server, expecting a `200` status code in response. If this is not provided, the request is rejected, and the actual `POST` request will not be processed.

To resolve this, you need to configure your server to handle `OPTIONS` requests properly. Below is an example configuration for Nginx. If you're using Apache, you will need to convert these settings to `.htaccess` rules.

## Plesk Panel (Apache and Nginx Settings)

### `config/cors.php`

```php
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | Cross-Origin Resource Sharing (CORS) Configuration
    |--------------------------------------------------------------------------
    |
    | Here you may configure your settings for cross-origin resource sharing
    | or "CORS". This determines what cross-origin operations may execute
    | in web browsers. You are free to adjust these settings as needed.
    |
    | To learn more: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
    |
    */

    'paths' => ['api/*', 'sanctum/csrf-cookie'],

    'allowed_methods' => ['*'],

    'allowed_origins' => ['*'],

    'allowed_origins_patterns' => [],

    'allowed_headers' => ['*'],

    'exposed_headers' => [],

    'max_age' => 0,

    'supports_credentials' => false,

];
```

### Laravel API Route (e.g., `mysite.com/api/check-token`)

```php
Route::post('/check-token', [TokenController::class, 'checkToken']);
```

### Nginx Configuration

```nginx
location /api/check-token {
    add_header 'Access-Control-Allow-Origin' '*';
    add_header 'Access-Control-Allow-Methods' '*';
    add_header 'Access-Control-Allow-Headers' 'Content-Type, Authorization';
    if ($request_method = OPTIONS ) {
        return 200;
    }
}
```

This configuration ensures that your server responds with a `200` status code to `OPTIONS` requests, allowing your `POST` requests to be processed correctly.

If you are using Apache, you will need to convert these settings into the equivalent `.htaccess` configuration.

I hope this helps!

