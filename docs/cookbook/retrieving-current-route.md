---
title: Retrieving Current Route
---

If you ever need to get access to the current route within your application all you have to do is call the request class' `getAttribute` method with an argument of `'route'` and it will return the current route, which is an instance of the `Slim\Route` class.

From there you can get the route's name by using `getName()` or get the methods supported by this route via `getMethods()`, etc.

 Note: If you need to access the route from within your app middleware you must set `'determineRouteBeforeAppMiddleware'` to true in your configuration otherwise `getAttribute('route')` will return null. Also `getAttribute('route')` will return null on non existent routes.

Example:
In your `config/databaseConfig.php`, add the following setting to the 'settings' option
```
$app = new App([
    'settings' => [
       'displayErrorDetails' => true,
        // Only set this if you need access to route within middleware
        'determineRouteBeforeAppMiddleware' => true //add this
    ]
]);
```


Then create a middleware file for it in `app/Middlewares/`

```
<?php 

namespace App\Middlewares;

class CurrentRouteMiddleware extends Middleware{
	
function __invoke(Request $request, Response $response, callable $next) {
    $route = $request->getAttribute('route');

    // return NotFound for non existent route
    if (empty($route)) {
        throw new NotFoundException($request, $response);
    }

    $name = $route->getName();
    $groups = $route->getGroups();
    $methods = $route->getMethods();
    $arguments = $route->getArguments();

    // do something with that information

    return $next($request, $response);
}
}
```

Then add it to the list of middlewares in `config/MiddlewareConfig.php` 

```
$app->add(new \App\Middlewares\CurrentRouteMiddleware($container));

```
