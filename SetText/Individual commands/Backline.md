# Backline

Does the same task than [line](Line.md), but with a different syntax and it only works in [Regular Letter Rendering](../Letter%20Rendering%20Methods/Regular%20Letter%20Rendering.md) while [line](Line.md) works for both letter rendering method.

## Syntax

(1)

````
|backline|
````

(2)

````
|backline,dummy,linespacing|
````

## Parameters

### `dummy`: var

The value of this parameter does not matter. It needs to be present because `linespacing` needs to be the second parameter to operate in syntax (2).

### `linespacing`:  float

A factor to multiply the standard line height to move below the current one. This value must be a valid float or an exception will be thrown. If this value is not specified, the default value is 1.0 (which will use the standard line spacing).

## Remarks

This command is basically a worse version of [line](Line.md) and is therefore not recommended to be used over it. Use [line](Line.md) instead.
