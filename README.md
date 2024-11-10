
<!--#echo json="package.json" key="name" underline="=" -->
split-options-from-data-items
=============================
<!--/#echo -->

<!--#echo json="package.json" key="description" -->
Treat some items of your mostly-data list (array or dictionary object) as
options that apply to the entire list.
<!--/#echo -->




API
---

This module exports one function:

### splitOptionsFromDataItems(opt)

`opt` is an optional options object as described below.

Returns a function `actuallySplitOptionsFromDataItems`
that takes one argument, `items`.


### actuallySplitOptionsFromDataItems(items)

`items` should be an array or a plain object.
It may also be false-y, in which case it returned verbatim immediately,
without any of the processing described below.

The "accumulator" will be explained below.
Usually, it is just an initially empty object.

Returns a shallow copy of `items`, except:

* If `items` itself is an object (including array objects)
  and has a truthy property whose name is
  the value of the `fxKey` option (see below),
  that property's value
  is deep-merged into the accumulator.
  The copy will omit that property.
* If an item is an object… (analogous to previous).
  * However, if the copy of the item would be empty
    as a result of the property omission,
    it will be omitted entirely.
* If an item is a string and begins with
  the value of the `fxKey` option (see below),
  followed by an "action symbol" (see below),
  the item will be omitted from the copy
  and a side effect is applied.
  * The available action symbols are:
    * U+003A colon (`:`) or U+007E tilde (`~`):
      The remainder is treated as the name of a flag.
      The such-named property in the accumulator
      is set to `true` for colon or `false` for tilde.
    * U+002B plus sign (`+`) or U+002D hyphen-minus (`-`):
      The remainder is treated as the name of a counter.
      Counters start at zero.
      The such-named property in the accumulator
      is increased by one for plus or decreased by one for minus.
    * U+003F question mark (`?`):
      The remainder is treated as a URL query string.
      It will be parsed using the `qrystr` module, and the result
      is deep-merged into the accumulator.


The options object `opt` that may have been given to the factory function
supports these optional keys:

* `fxKey`: A string that distinguishes options from regular data,
  as explained above.
  Default: `'\fx'` (U+000C form feed, U+0078 latin small letter x)
* `accum`: The accumulator to use. If false-y, a new empty object will be
  used instead.
* `store`: Which property of the copy shall hold the accumulator.
    Default: `0` if `items` is an array, `''` (empty string) otherwise.
  * It is assigned before copying begins, so that in case of an array you can
    choose `0` to reliably have the accumulator be the first item.
  * It is assigned again after copying, so that in case of a dictionary object,
    the accumulator wins any name potential conflict that may have occurred
    during copying.
  * The special value `null` means that the accumulator shall not be stored
    into the copy of `items`.
    Usually in this case you'll want to provide a pre-made accumulator.


### actuallySplitOptionsFromDataItems.accum(reset)

Returns the current or last-used accumulator, or `false` if none was used.
This method is prone to race conditions and recursion,
so use it carefully, if at all.
Usually it is better to provide a pre-made accumulator if you
don't want to store it on the copy.

The `reset` argument is optional. If truthy, the current accumulator
is replaced by that value.
This is even more prone to race conditions and recursion,
and useful only in exotic scenarios.




Usage
-----

see [test/usage.mjs](test/usage.mjs).


<!--#toc stop="scan" -->



Known issues
------------

* Needs more/better tests and docs.




&nbsp;


License
-------
<!--#echo json="package.json" key=".license" -->
ISC
<!--/#echo -->
