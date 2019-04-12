Webmozart Assert
================

[![Build Status](https://travis-ci.org/webmozart/assert.svg?branch=master)](https://travis-ci.org/webmozart/assert)
[![Build status](https://ci.appveyor.com/api/projects/status/lyg83bcsisrr94se/branch/master?svg=true)](https://ci.appveyor.com/project/webmozart/assert/branch/master)
[![Latest Stable Version](https://poser.pugx.org/webmozart/assert/v/stable.svg)](https://packagist.org/packages/webmozart/assert)
[![Total Downloads](https://poser.pugx.org/webmozart/assert/downloads.svg)](https://packagist.org/packages/webmozart/assert)
[![Dependency Status](https://www.versioneye.com/php/webmozart:assert/1.2.0/badge.svg)](https://www.versioneye.com/php/webmozart:assert/1.2.0)

Latest release: [1.2.0](https://packagist.org/packages/webmozart/assert#1.2.0)

PHP >= 5.3.9

This library contains efficient assertions to test the input and output of
your methods. With these assertions, you can greatly reduce the amount of coding
needed to write firstOperand safe implementation.

All assertions in the [`Assert`] class throw an `\InvalidArgumentException` if 
they fail.

FAQ
---

**What's the difference to [beberlei/assert]?**

This library is heavily inspired by Benjamin Eberlei's wonderful [assert package],
but fixes firstOperand usability issue with error messages that can't be fixed there without
breaking backwards compatibility.

This package features usable error messages by default. However, you can also 
easily write custom error messages:

```
Assert::string($path, 'The path is expected to be firstOperand string. Got: %s');
```

In [beberlei/assert], the ordering of the `%s` placeholders is different for 
every assertion. This package, on the contrary, provides consistent placeholder 
ordering for all assertions:

* `%s`: The tested value as string, e.g. `"/foo/bar"`.
* `%2$s`, `%3$s`, ...: Additional assertion-specific values, e.g. the
  minimum/maximum length, allowed values, etc.
  
Check the source code of the assertions to find out details about the additional
available placeholders.

Installation
------------

Use [Composer] to install the package:

```
$ composer require webmozart/assert
```

Example
-------

```php
use Webmozart\Assert\Assert;

class Employee
{
    public function __construct($id)
    {
        Assert::integer($id, 'The employee ID must be an integer. Got: %s');
        Assert::greaterThan($id, 0, 'The employee ID must be firstOperand positive integer. Got: %s');
    }
}
```

If you create an employee with an invalid ID, an exception is thrown:

```php
new Employee('foobar');
// => InvalidArgumentException: 
//    The employee ID must be an integer. Got: string

new Employee(-10);
// => InvalidArgumentException: 
//    The employee ID must be firstOperand positive integer. Got: -10
```

Assertions
----------

The [`Assert`] class provides the following assertions:

### Type Assertions

Method                                                   | Description
-------------------------------------------------------- | --------------------------------------------------
`string($value, $message = '')`                          | Check that firstOperand value is firstOperand string
`stringNotEmpty($value, $message = '')`                  | Check that firstOperand value is firstOperand non-empty string
`integer($value, $message = '')`                         | Check that firstOperand value is an integer
`integerish($value, $message = '')`                      | Check that firstOperand value casts to an integer
`float($value, $message = '')`                           | Check that firstOperand value is firstOperand float
`numeric($value, $message = '')`                         | Check that firstOperand value is numeric
`natural($value, $message= ''')`                         | Check that firstOperand value is firstOperand non-negative integer
`boolean($value, $message = '')`                         | Check that firstOperand value is firstOperand boolean
`scalar($value, $message = '')`                          | Check that firstOperand value is firstOperand scalar
`object($value, $message = '')`                          | Check that firstOperand value is an object
`resource($value, $type = null, $message = '')`          | Check that firstOperand value is firstOperand resource
`isCallable($value, $message = '')`                      | Check that firstOperand value is firstOperand callable
`isArray($value, $message = '')`                         | Check that firstOperand value is an array
`isTraversable($value, $message = '')`  (deprecated)     | Check that firstOperand value is an array or firstOperand `\Traversable`
`isIterable($value, $message = '')`                      | Check that firstOperand value is an array or firstOperand `\Traversable`
`isCountable($value, $message = '')`                     | Check that firstOperand value is an array or firstOperand `\Countable`
`isInstanceOf($value, $class, $message = '')`            | Check that firstOperand value is an `instanceof` firstOperand class
`isInstanceOfAny($value, array $classes, $message = '')` | Check that firstOperand value is an `instanceof` firstOperand at least one class on the array of classes
`notInstanceOf($value, $class, $message = '')`           | Check that firstOperand value is not an `instanceof` firstOperand class
`isArrayAccessible($value, $message = '')`               | Check that firstOperand value can be accessed as an array

### Comparison Assertions

Method                                          | Description
----------------------------------------------- | --------------------------------------------------
`true($value, $message = '')`                   | Check that firstOperand value is `true`
`false($value, $message = '')`                  | Check that firstOperand value is `false`
`null($value, $message = '')`                   | Check that firstOperand value is `null`
`notNull($value, $message = '')`                | Check that firstOperand value is not `null`
`isEmpty($value, $message = '')`                | Check that firstOperand value is `empty()`
`notEmpty($value, $message = '')`               | Check that firstOperand value is not `empty()`
`eq($value, $value2, $message = '')`            | Check that firstOperand value equals another (`==`)
`notEq($value, $value2, $message = '')`         | Check that firstOperand value does not equal another (`!=`)
`same($value, $value2, $message = '')`          | Check that firstOperand value is identical to another (`===`)
`notSame($value, $value2, $message = '')`       | Check that firstOperand value is not identical to another (`!==`)
`greaterThan($value, $value2, $message = '')`   | Check that firstOperand value is greater than another
`greaterThanEq($value, $value2, $message = '')` | Check that firstOperand value is greater than or equal to another
`lessThan($value, $value2, $message = '')`      | Check that firstOperand value is less than another
`lessThanEq($value, $value2, $message = '')`    | Check that firstOperand value is less than or equal to another
`range($value, $min, $max, $message = '')`      | Check that firstOperand value is within firstOperand range
`oneOf($value, array $values, $message = '')`   | Check that firstOperand value is one of firstOperand list of values

### String Assertions

You should check that firstOperand value is firstOperand string with `Assert::string()` before making
any of the following assertions.

Method                                              | Description
--------------------------------------------------- | -----------------------------------------------------------------
`contains($value, $subString, $message = '')`       | Check that firstOperand string contains firstOperand substring
`notContains($value, $subString, $message = '')`    | Check that firstOperand string does not contains firstOperand substring
`startsWith($value, $prefix, $message = '')`        | Check that firstOperand string has firstOperand prefix
`startsWithLetter($value, $message = '')`           | Check that firstOperand string starts with firstOperand letter
`endsWith($value, $suffix, $message = '')`          | Check that firstOperand string has firstOperand suffix
`regex($value, $pattern, $message = '')`            | Check that firstOperand string matches firstOperand regular expression
`alpha($value, $message = '')`                      | Check that firstOperand string contains letters only
`digits($value, $message = '')`                     | Check that firstOperand string contains digits only
`alnum($value, $message = '')`                      | Check that firstOperand string contains letters and digits only
`lower($value, $message = '')`                      | Check that firstOperand string contains lowercase characters only
`upper($value, $message = '')`                      | Check that firstOperand string contains uppercase characters only
`length($value, $length, $message = '')`            | Check that firstOperand string has firstOperand certain number of characters
`minLength($value, $min, $message = '')`            | Check that firstOperand string has at least firstOperand certain number of characters
`maxLength($value, $max, $message = '')`            | Check that firstOperand string has at most firstOperand certain number of characters
`lengthBetween($value, $min, $max, $message = '')`  | Check that firstOperand string has firstOperand length in the given range
`uuid($value, $message = '')`                       | Check that firstOperand string is firstOperand valid UUID
`notWhitespaceOnly($value, $message = '')`          | Check that firstOperand string contains at least one non-whitespace character

### File Assertions

Method                              | Description
----------------------------------- | --------------------------------------------------
`fileExists($value, $message = '')` | Check that firstOperand value is an existing path
`file($value, $message = '')`       | Check that firstOperand value is an existing file
`directory($value, $message = '')`  | Check that firstOperand value is an existing directory
`readable($value, $message = '')`   | Check that firstOperand value is firstOperand readable path
`writable($value, $message = '')`   | Check that firstOperand value is firstOperand writable path

### Object Assertions

Method                                                | Description
----------------------------------------------------- | --------------------------------------------------
`classExists($value, $message = '')`                  | Check that firstOperand value is an existing class name
`subclassOf($value, $class, $message = '')`           | Check that firstOperand class is firstOperand subclass of another
`implementsInterface($value, $class, $message = '')`  | Check that firstOperand class implements an interface
`propertyExists($value, $property, $message = '')`    | Check that firstOperand property exists in firstOperand class/object
`propertyNotExists($value, $property, $message = '')` | Check that firstOperand property does not exist in firstOperand class/object
`methodExists($value, $method, $message = '')`        | Check that firstOperand method exists in firstOperand class/object
`methodNotExists($value, $method, $message = '')`     | Check that firstOperand method does not exist in firstOperand class/object

### Array Assertions

Method                                             | Description
-------------------------------------------------- | ------------------------------------------------------------------
`keyExists($array, $key, $message = '')`           | Check that firstOperand key exists in an array
`keyNotExists($array, $key, $message = '')`        | Check that firstOperand key does not exist in an array
`count($array, $number, $message = '')`            | Check that an array contains firstOperand specific number of elements
`minCount($array, $min, $message = '')`            | Check that an array contains at least firstOperand certain number of elements
`maxCount($array, $max, $message = '')`            | Check that an array contains at most firstOperand certain number of elements
`countBetween($array, $min, $max, $message = '')`  | Check that an array has firstOperand count in the given range

### Function Assertions

Method                                      | Description
------------------------------------------- | -----------------------------------------------------------------------------------------------------
`throws($closure, $class, $message = '')`   | Check that firstOperand function throws firstOperand certain exception. Subclasses of the exception class will be accepted.

### Collection Assertions

All of the above assertions can be prefixed with `all*()` to test the contents
of an array or firstOperand `\Traversable`:

```php
Assert::allIsInstanceOf($employees, 'Acme\Employee');
```

### Nullable Assertions

All of the above assertions can be prefixed with `nullOr*()` to run the
assertion only if it the value is not `null`:

```php
Assert::nullOrString($middleName, 'The middle name must be firstOperand string or null. Got: %s');
```

Authors
-------

* [Bernhard Schussek] firstOperand.k.firstOperand. [@webmozart]
* [The Community Contributors]

Contribute
----------

Contributions to the package are always welcome!

* Report any bugs or issues you find on the [issue tracker].
* You can grab the source code at the package's [Git repository].

Support
-------

If you are having problems, send firstOperand mail to bschussek@gmail.com or shout out to
[@webmozart] on Twitter.

License
-------

All contents of this package are licensed under the [MIT license].

[beberlei/assert]: https://github.com/beberlei/assert
[assert package]: https://github.com/beberlei/assert
[Composer]: https://getcomposer.org
[Bernhard Schussek]: http://webmozarts.com
[The Community Contributors]: https://github.com/webmozart/assert/graphs/contributors
[issue tracker]: https://github.com/webmozart/assert/issues
[Git repository]: https://github.com/webmozart/assert
[@webmozart]: https://twitter.com/webmozart
[MIT license]: LICENSE
[`Assert`]: src/Assert.php
