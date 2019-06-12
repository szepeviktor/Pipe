# Pipe

Object-oriented pipe operator implementation based 
on [RFC Pipe Operator](https://wiki.php.net/rfc/pipe-operator).


## Installation

Library can be installed into any PHP application 
using `Composer` dependency manager.

```sh
$ composer require serafim/pipe
```

In order to access pipe library make sure to include `vendor/autoload.php` 
in your file.

```php
<?php

require __DIR__ . '/vendor/autoload.php';
```

## Usage

A common PHP OOP pattern is the use of method chaining, or what is 
also known as "Fluent Expressions". So named for the way one method 
flows into the next to form a conceptual hyper-expression.

However, when using the functional approach this can lead to reduced 
readability, polluted symbol tables, or static-analysis defying 
type inconsistency such as in the following example:

```php
<?php

$snakeCase = strtolower(
    preg_replace('/(.)(?=[A-Z])/u', '$1_', 
        preg_replace('/\s+/u', '', 
            ucwords('HelloWorld')
        )
    )
);
             
var_dump($snakeCase); // "hello_world"
```

The pipe library fixes this problem, allows you to 
chain the execution of pure functions:

```php
$snakeCase = pipe($camelCase)
    ->ucwords(_)
    ->pregReplace('/\s+/u', '', _)
    ->pregReplace('/(.)(?=[A-Z])/u', '$1_', _)
    ->strToLower(_)
    ->varDump;
```

All functions are available both in the form of **camelCase**, 
and in the form of a **snake_case**:
  
```php
pipe($value)->var_dump;
 
// same as
pipe($value)->varDump;
```

and

```php
pipe($value)->strtolower;
 
// same as
pipe($value)->strToLower;

// same as
pipe($value)->str_to_lower;
```

### Another Example

See: [https://wiki.php.net/rfc/pipe-operator#file_collection_example](https://wiki.php.net/rfc/pipe-operator#file_collection_example)

```php
<?php
$result = array_merge(
    $result,
    namespaced\func\get_file_arg(
        array_map(
            function ($x) use ($arg) {
                return $arg . '/' . $x;
            },
            array_filter(
                scandir($arg),
                function ($x) {
                    return $x !== '.' && $x !== '..';
                }
            )
        )
    )
);
```

With this library, the above could be easily rewritten as:

```php
<?php

$result = pipe($arg)
    ->scandir($arg)
    ->arrayFilter(_, fn($x) => $x !== '.' && $x != '..')
    ->arrayMap(fn($x) => $arg . '/' . $x, _)
    ->using('namespaced\func')->get_file_arg
    ->arrayMerge($result, _);
```


## Working With Value

To pass a value as an argument to a function, use the 
underscore (`_`) character:

```php
<?php

pipe('hello')
    ->strReplace('o', '', _)
    ->varDump; // "hell"
```

You can omit parentheses if only one argument is used:

```php
<?php

pipe('some')->isArray->varDump; // bool(false) 
```

To get the value, use one of the options:

```php
<?php
$pipe = pipe('hello')->strToUpper;

// Using "value" property
$result = $pipe->value; // string("HELLO")

// Using pipe invocation
$result = $pipe(); // string("HELLO")
```
