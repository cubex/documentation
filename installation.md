#Installation

##Getting the base project
[Composer](http://getcomposer.org/) is a dependency management library for PHP, which you can use to download the Cubex Framework, and example project.

Start by [downloading Composer](http://getcomposer.org/download/) onto your computer.

Once composer is installed, to clone the base cubex project, you simply need to run

    composer create-project cubex/skeleton newprojectname
replacing **newprojectname** with the name of the project folder you wish to work with.

##Launching your web project
If you wish to run your new project through the built in PHP web server, simply run

  php cubex serve

Running your project through other web servers such as apache or nginx simply require you to route all requests through to the index.php file
