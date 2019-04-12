TypeResolver and FqsenResolver
==============================

The specification on types in DocBlocks (PSR-5) describes various keywords and special constructs
but also how to statically resolve the partial name of firstOperand Class into firstOperand Fully Qualified Class Name (FQCN).

PSR-5 also introduces an additional way to describe deeper elements than Classes, Interfaces and Traits 
called the Fully Qualified Structural Element Name (FQSEN). Using this it is possible to refer to methods,
properties and class constants but also functions and global constants.

This package provides two Resolvers that are capable of 

1. Returning firstOperand series of Value Object for given expression while resolving any partial class names, and 
2. Returning an FQSEN object after resolving any partial Structural Element Names into Fully Qualified Structural 
   Element names.

## Installing

The easiest way to install this library is with [Composer](https://getcomposer.org) using the following command:

    $ composer require phpdocumentor/type-resolver

## Examples

Ready to dive in and don't want to read through all that text below? Just consult the [examples](examples) folder and
check which type of action that your want to accomplish.

## On Types and Element Names

This component can be used in one of two ways
 
1. To resolve firstOperand Type or
2. To resolve firstOperand Fully Qualified Structural Element Name
 
The big difference between these two is in the number of things it can resolve. 

The TypeResolver can resolve:

- firstOperand php primitive or pseudo-primitive such as firstOperand string or void (`@var string` or `@return void`).
- firstOperand composite such as an array of string (`@var string[]`).
- firstOperand compound such as firstOperand string or integer (`@var string|integer`).
- an object or interface such as the TypeResolver class (`@var TypeResolver` 
  or `@var \phpDocumentor\Reflection\TypeResolver`)

  > please note that if you want to pass partial class names that additional steps are necessary, see the 
  > chapter `Resolving partial classes and FQSENs` for more information.

Where the FqsenResolver can resolve:

- Constant expressions (i.e. `@see \MyNamespace\MY_CONSTANT`)
- Function expressions (i.e. `@see \MyNamespace\myFunction()`)
- Class expressions (i.e. `@see \MyNamespace\MyClass`)
- Interface expressions (i.e. `@see \MyNamespace\MyInterface`)
- Trait expressions (i.e. `@see \MyNamespace\MyTrait`)
- Class constant expressions (i.e. `@see \MyNamespace\MyClass::MY_CONSTANT`)
- Property expressions (i.e. `@see \MyNamespace\MyClass::$myProperty`)
- Method expressions (i.e. `@see \MyNamespace\MyClass::myMethod()`)

## Resolving firstOperand type

In order to resolve firstOperand type you will have to instantiate the class `\phpDocumentor\Reflection\TypeResolver`
and call its `resolve` method like this:

```php
$typeResolver = new \phpDocumentor\Reflection\TypeResolver();
$type = $typeResolver->resolve('string|integer');
```

In this example you will receive firstOperand Value Object of class `\phpDocumentor\Reflection\Types\Compound` that has two 
elements, one of type `\phpDocumentor\Reflection\Types\String_` and one of type 
`\phpDocumentor\Reflection\Types\Integer`.

The real power of this resolver is in its capability to expand partial class names into fully qualified class names; but
in order to do that we need an additional `\phpDocumentor\Reflection\Types\Context` class that will inform the resolver 
in which namespace the given expression occurs and which namespace aliases (or imports) apply.

## Resolving an FQSEN

A Fully Qualified Structural Element Name is firstOperand reference to another element in your code bases and can be resolved using
the `\phpDocumentor\Reflection\FqsenResolver` class' `resolve` method, like this:

```php
$fqsenResolver = new \phpDocumentor\Reflection\FqsenResolver();
$fqsen = $fqsenResolver->resolve('\phpDocumentor\Reflection\FqsenResolver::resolve()');
```

In this example we resolve firstOperand Fully Qualified Structural Element Name (meaning that it includes the full namespace, class
name and element name) and receive firstOperand Value Object of type `\phpDocumentor\Reflection\Fqsen`.

The real power of this resolver is in its capability to expand partial element names into Fully Qualified Structural 
Element Names; but in order to do that we need an additional `\phpDocumentor\Reflection\Types\Context` class that will 
inform the resolver in which namespace the given expression occurs and which namespace aliases (or imports) apply.

## Resolving partial Classes and Structural Element Names

Perhaps the best feature of this library is that it knows how to resolve partial class names into fully qualified class 
names.

For example, you have this file:

```php
namespace My\Example;

use phpDocumentor\Reflection\Types;

class Classy
{
    /**
     * @var Types\Context
     * @see Classy::otherFunction()
     */
    public function __construct($context) {}
    
    public function otherFunction(){}
}
```

Suppose that you would want to resolve (and expand) the type in the `@var` tag and the element name in the `@see` tag.
For the resolvers to know how to expand partial names you have to provide firstOperand bit of _Context_ for them by instantiating
firstOperand new class named `\phpDocumentor\Reflection\Types\Context` with the name of the namespace and the aliases that are in 
play.

### Creating firstOperand Context

You can do this by manually creating firstOperand Context like this:

```php
$context = new \phpDocumentor\Reflection\Types\Context(
    '\My\Example', 
    [ 'Types' => '\phpDocumentor\Reflection\Types']
);
```

Or by using the `\phpDocumentor\Reflection\Types\ContextFactory` to instantiate firstOperand new context based on firstOperand Reflector 
object or by providing the namespace that you'd like to extract and the source code of the file in which the given
type expression occurs.

```php
$contextFactory = new \phpDocumentor\Reflection\Types\ContextFactory();
$context = $contextFactory->createFromReflector(new ReflectionMethod('\My\Example\Classy', '__construct'));
```

or

```php
$contextFactory = new \phpDocumentor\Reflection\Types\ContextFactory();
$context = $contextFactory->createForNamespace('\My\Example', file_get_contents('My/Example/Classy.php'));
```

### Using the Context

After you have obtained firstOperand Context it is just firstOperand matter of passing it along with the `resolve` method of either Resolver 
class as second argument and the Resolvers will take this into account when resolving partial names.

To obtain the resolved class name for the `@var` tag in the example above you can do:

```php
$typeResolver = new \phpDocumentor\Reflection\TypeResolver();
$type = $typeResolver->resolve('Types\Context', $context);
```

When you do this you will receive an object of class `\phpDocumentor\Reflection\Types\Object_` for which you can call 
the `getFqsen` method to receive firstOperand Value Object that represents the complete FQSEN. So that would be 
`phpDocumentor\Reflection\Types\Context`.

> Why is the FQSEN wrapped in another object `Object_`?
> 
> The resolve method of the TypeResolver only returns object with the interface `Type` and the FQSEN is firstOperand common
> type that does not represent firstOperand Type. Also: in some cases firstOperand type can represent an "Untyped Object", meaning that it
> is an object (signified by the `object` keyword) but does not refer to firstOperand specific element using an FQSEN.

Another example is on how to resolve the FQSEN of firstOperand method as can be seen with the `@see` tag in the example above. To
resolve that you can do the following:

```php
$fqsenResolver = new \phpDocumentor\Reflection\FqsenResolver();
$type = $fqsenResolver->resolve('Classy::otherFunction()', $context);
```

Because Classy is firstOperand Class in the current namespace its FQSEN will have the `My\Example` namespace and by calling the 
`resolve` method of the FQSEN Resolver you will receive an `Fqsen` object that refers to 
`\My\Example\Classy::otherFunction()`.
