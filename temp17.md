PHP tips collected from work (1)
==============

#### 1. powerful reflection functions

*	**Problem**: I want to find the definition of a function, which binds to a plugin, and var_dump, var_expore do not work. so i have no clue where the function definition is. that sucks.
*	**Solution**: Then there is reflection to help me out. now at least i know where to find the function definition. [reference](http://stackoverflow.com/questions/2222142/how-to-find-out-where-a-function-is-defined).

	```php
	$reflFunc = new ReflectionFunction('function_name');
	print $reflFunc->getFileName() . ':' . $reflFunc->getStartLine();
	```

*	**Extra**: How to find all public methods of an object or a class? [reference](http://stackoverflow.com/a/11575845)

	```php
 	$class = new \ReflectionClass('full\class\path or object');
	$methods = $class->getMethods(\ReflectionMethod::IS_PUBLIC);
	var_dump($methods);
	```

#### 2. what's the difference between psr-0 and psr-4?
*	psr-4 needs namespace in each file to be able to work.
*	This is pretty self explanatory, it simply tells the autoloader that `app/lib/MyCompany` is the root for the `MyCompany\` namespace:
	```
	{
	    "autoload": {
	        "psr-4": {
	            "MyCompany\\": "app/lib/MyCompany/",
	        }
	    }
	}
	```
*	psr-0 and psr-4 switch example:
	```
	// before
	{
	    "autoload": {
	        "psr-0": {
	            "Foo\\Bar\\": "src/"
	        }
	    }
	}

	// after
	{
	    "autoload": {
	        "psr-4": {
	            "Foo\\Bar\\": "src/Foo/Bar/"
	        }
	    }
	}
	```
*	use psr-4 only


#### 3. composer: how to autoload classes in your app?
*	use psr-4 for mapping namespace to folder
*	The namespace has to end with \\. For example, `"Acme\\"`.
*	The namespace Acme points to the src/ directory.
*	important: update composer to let it know newly added classes: `composer dumpautoload -o`, or just `composer update`
*	last step: Import the namespace to your scripts. For example, in your entry script `index.php`, add `require "/path/to/vendor/autoload.php";` before you `use` any namespaced classes!
*	[reference](http://phpenthusiast.com/blog/how-to-autoload-with-composer)

#### 4. composer again: how to use imported third party library in your app?
*	after you do `composer install or update`, if you go check `composer/autoload_classmap.php` file, all the keys are shortcuts to actual file paths. so you can directly use those "keys" as classnames in your php script.
*	example: `$foo = new \VendorClass;`  [ref](http://stackoverflow.com/questions/27038840/adding-namespace-to-imported-third-party-package-in-composer)

#### 5. then: how to set up phpunit correctly with composer?
*	apply the same namespace conventions. psr-4, ect
*	we need to bootstrap a script to import composer's `autoload.php` file before running any test files. to do that, use this code `<phpunit colors="true" bootstrap="phpunit-bootstrap.php">` in phpunit.xml file, and create `phpunit-bootstrap.php` file with this line of code: `require "/path/to/vendor/autoload.php" ` [reference](http://blog.shamess.info/2013/05/22/phpunit-composer-autoload-bootstrap/)
*	Or even better, directly include the `vendor/autoload.php` in bootstrap attribute!
`<phpunit bootstrap="./vendor/autoload.php">` [reference](http://stackoverflow.com/a/15711324/1369136). I will use this one from now on!

Tags: PHP Reflection, PSR-0 vs  PSR-4, Composer