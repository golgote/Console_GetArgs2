Command-line arguments parsing class
====================================

This implementation was freely inspired by a python module called getargs by Vinod Vijayarajan and a perl CPAN module called Getopt::Simple by Ron Savage

This class implements a Command Line Parser that your cli applications can use to parse command line arguments found in $_SERVER['argv'] or a user defined array.

It gives more flexibility and error checking than Console_Getopt. It also performs some arguments validation and is capable to return a formatted help text to the user, based on the configuration it is given.

The class provides the following capabilities:

- Each command line option can take an arbitrary number of arguments.
- Makes the distinction between switches (options without arguments) and options that require arguments.
- Recognizes 'single-argument-options' and 'default-if-set' options.
- Switches and options with arguments can be interleaved in the command line.
- You can specify the maximum and minimum number of arguments an option can take. Use -1 if you don't want to specify an upper bound.
- Specify the default arguments to an option
- Short options can be more than one letter in length.
- A given option may be invoked by multiple names (aliases).
- Understands by default the --help, -h options
- Can return a formatted help text
- Arguments may be specified using the '=' syntax also.
- Short option names may be concatenated (-dvw 100 == -d -v -w 100)
- Can define a default option that will take any arguments added without an option name
- Can pass in a user defined array of arguments instead of using $_SERVER['argv']

Usage
-----

The constructor will return a new Console_Getargs2 object built using the given configuration options. If the configuration or the command line options contain errors, the returned object will in fact be an Exception explaining the cause of the error.

Factory expects an array as parameter.

The format for this array is:

```php
array(
 longname => array('short'   => Short option name,
                   'max'     => Maximum arguments for option,
                   'min'     => Minimum arguments for option,
                   'default' => Default option argument,
                   'desc'    => Option description)
)
```

If an option can be invoked by more than one name, they have to be defined by using | as a separator.
For example: name1|name2
This works both in long and short names.

max/min are the most/least number of arguments an option accepts.

The 'defaults' field is optional and is used to specify default arguments to an option. These will be assigned to the option if it is *not* used in the command line.
Default arguments can be:

- a single value for options that require a single argument,
- an array of values for options with more than one possible arguments.

Default argument(s) are mandatory for 'default-if-set' options.

If max is 0 (option is just a switch), min is ignored.
If max is -1, then the option can have an unlimited number of arguments greater or equal to min.

If max == min == 1, the option is treated as a single argument option.

If max >= 1 and min == 0, the option is treated as a 'default-if-set' option. This implies that it will get the default argument only if the option is used in the command line without any value. (Note: defaults *must* be specified for 'default-if-set' options)

If the option is not in the command line, the defaults are *not* applied. If an argument for the option is specified on the command line, then the given argument is assigned to the option.
Thus:

- a --debug in the command line would cause debug = 'default argument'
- a --debug 2 in the command line would result in debug = 2
 if not used in the command line, debug will not be defined.

### Example 1

```php
require_once 'Console/Getargs2.php';
try {
  $args = new Console_Getargs2($config);
} catch (Console_GetArgs2_UserException $e) {
  echo $e->getHelp();
} catch (Console_GetArgs2_Exception $e) {
  echo $e->getMessage();
}
echo 'Verbose: '.$args['verbose']."\n";
if (isset($args['bs'])) {
 echo 'Block-size: '.(is_array($args['bs']) ? implode(', ', $args['bs'])."\n" : $args['bs']."\n");
} else {
 echo "Block-size: undefined\n";
}
echo 'Files: '.(isset($args['file']) ? implode(', ', $args['file'])."\n" : "undefined\n");
if (isset($args['n'])) {
 echo 'Nodes: '.(is_array($args['n']) ? implode(', ', $args['n'])."\n" : $args['n']."\n");
} else {
 echo "Nodes: undefined\n";
}
echo 'Log: '.$args['log']."\n";
echo 'Debug: '.(isset($args['d']) ? "YES\n" : "NO\n");
```

If you don't want to require any option name for a set of arguments, or if you would like any "leftover" arguments assigned by default, you can create an option named CONSOLE_GETARGS_PARAMS that will grab any arguments that cannot be assigned to another option. The rules for CONSOLE_GETARGS_PARAMS are still the same. If you specify that two values must be passed then two values must be passed. See the example script for a complete example.

More examples : [https://github.com/pear/Console_Getargs/tree/master/examples](https://github.com/pear/Console_Getargs/tree/master/examples)
