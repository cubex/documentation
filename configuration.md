#Configuration Options

Config options can be set in your config file of choice, the examples below are ini based examples.
The ini group represents a config section, and the values represent the config items in that section.

###Kernel

####Define your project entry kernel
Instance Of: \Cubex\Kernel\CubexKernel

    [kernel]
    default = \YourNamespace\YourProject

###Routing

####Changing the default router
Instance Of: \Cubex\Routing\IRouter

    [routing]
    router = \Cubex\Routing\Router

####Enable automatic routing by available methods
This will only run if no defined route can be found

    [routing]
    auto = bool

###Errors

####Setting your standard 404 error
Instance Of: \Symfony\Component\HttpFoundation\Response

    [errors]
    404 = \Cubex\Responses\Error404Response
