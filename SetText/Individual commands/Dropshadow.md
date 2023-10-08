# Dropshadow

Set the text's drop shadow offset vector or turn off the effect.

## Syntax

(1) (To turn off drop shadows effect)

````
|dropshadow|
````

(2)

````
|dropshadow,size|
````

(3)

````
|dropshadow,sizex,sizey|
````

## Parameters

### `size`: float

The size in x and y to use for the vector of the dropshadow. This must be a valid float or an exception will be thrown. This will make a drop shadow positioned to the right and down by the value.

### `sizex`: float

The horizontal offset to use for the vector of the dropshadow. This must be a valid float or an exception will be thrown.

### `sizey`: float

The vertical offset to use for the vector of the dropshadow. This must be a valid float or an exception will be thrown.

## Remarks

The drop shadows has slightly different behavior depending on the letter rendering method, but this command only changes its state.

In [Regular Letter Rendering](../Letter%20Rendering%20Methods/Regular%20Letter%20Rendering.md), drop shadows uses a separate letter slot per letter to render a pure black, half transparent version of the same letter, but offset by the vector specified by this command.

In [Single Letter Rendering](../Letter%20Rendering%20Methods/Single%20Letter%20Rendering.md), this is instead done by using a second letter slot as opposed to one per line to do the same effect.

In [Regular Letter Rendering](../Letter%20Rendering%20Methods/Regular%20Letter%20Rendering.md), It also supports [fadeletter](Fadeletter.md) which will render the drop shadow letter in a fully transparent color, but progressively go to pure black, half transparent in the course of 200 frames.
