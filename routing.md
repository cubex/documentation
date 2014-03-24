#Routing In Cubex Kernels

## Routing Process
1. Process provided routes
2. Attempt to automatically route
3. Process the default action

## Provided Routes
Within your class, you should have a method named getRoutes() which returns an array.  This array is populated with an array view of your routes for the class.

A basic hello world example:

    public function getRoutes()
    {
      return [
        'hello/world' => 'helloWorld', //Run the renderHello method
      ];
    }

When Cubex routes this class, it will match the each route against the url, and when it locates a match, will execute the route.



## Route Execution
When processing the route result, the following checks (in order) will be run, and the relevant result returned.

1. A Response object returned from your route will return straight back
2. Scalar Type
  * Match for a class
  * Check to see if the result is callable, and if so, return the result of the callable
  * Match for a URL pattern, and return a redirect response if true.
  * Search for an available method, and call that method returning the response
3. A HttpKernelInterface value will have the handle() method called with the current request and catch types
4. A callable will be executed and returned
5. An object supporting __toString will be returned as a string

### String based callable
You can provide callable strings, splitting the class and method on either the "," or "@" characters.

### URL Redirection
If you want your route straight onto a new url, you can specify your route in any of the following ways

* http://domain.tld - 302 redirect to domain.tld
* https://domain.tld - 302 redirect to domain.tld (SSL)
* @301!http://domain.tld - 301 redirect to domain.tld
* #@/homepage - 302 redirect to /homepage
* #@homepage - 302 redirect to homepage

### Attempting Methods
Methods will be attempted in the following order

1. If the request is an Ajax/XmlHttp request: ajax$method
2. If the request has a method of POST: post$method
3. render$method
4. action$method
5. $method

e.g ajaxHelloWorld(), postHelloWorld(), renderHelloWorld(), actionHelloWorld(), helloWorld()

When calling a method, we automatically extract 2 outputs, one if the return from the method, and the second is the raw output from your method (echo, print, etc).

If your method returns a valid Response object, that will be pushed back up the stack for returning.
If you have returned any other value, a new Cubex Response will be generated, with your output being sent to the constructor.  This will automatically generate json responses for arrays and objects, html for scalar values etc.
If no value is returned from your method, a new Cubex Response will be created with the output captured while your method executed.

## Automatic Routing
When automatically routing, the specified url will be split into its parts, and the first part will be attempted as a method.  All remaining parts of the url will be passed through to your method as parameters.

e.g. for url /hello/world/today

the following method would be called

    function renderHello($who,$when="on this fine day")
    {
      return "Hello $who, how are you $when";
    }

You should expect the following output

    /hello/world/ => "Hello World, how are you on this fine day"
    /hello/world/today => "Hello World, how are you today"
    /hello => "Hello , how are you on this fine day"

### Multi Level Automatic Routing
For each CubexKernel that is automatically routed, if not method can be found, a sub route will be located.  The patterns to which your request is routed will be based on the path you are trying to access and the Kernel type you are extending.

The class name will be based on your access path, and the level you are within.
e.g. /categories/pets/dog
Will split down to
1. categories
2. pets
3. dog

Depending on the kernel type your class extends, the sub route will search for a class following one of the files.  The sub classes that are searched for are defined within the subRouteTo() method of your class, meaning you can define whatever code structure you like.  %s is replaced with the ucfirst() version of your path, e.g. categories will become Categories.

The baseNamespace is taken from the namespace of the class you started auto routing at.

####ProjectKernel

    return [
      'Applications\%s\%sApplication',
      'Applications\%s\%sApp',
      'Applications\%sApplication',
      'Applications\%sApp',
      '%sApplication',
      '%sApp',
      '%s',
    ];

In the example, this will translate to (assuming first class rule hit):
\BaseNamespace\Applications\Categories\CategoriesApplication

####ApplicationKernel

    return [
      'Controllers\%s\%sController',
      'Controllers\%sController',
      'Controllers\%s\%s',
      'Controllers\%s',
      '%sController',
      '%s',
    ];

In the example, this will translate to (assuming second class rule hit):
\BaseNamespace\Applications\Categories\Controllers\PetsController

####ControllerKernel

    return [
      'Views\%sView',
      'Views\%s',
      '%sView',
      '%s',
    ];

In the example, this will translate to (assuming first class rule hit):
\BaseNamespace\Applications\Categories\Controllers\Views\DogView

####Custom Patterns
Of course, if you don't like the pre-defined styles, you can simply define your own return array, and route to whatever class you like.

    return [
      'Bubbles\%sBubble',
    ];
