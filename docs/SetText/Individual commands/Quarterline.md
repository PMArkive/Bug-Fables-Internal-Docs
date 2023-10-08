# Quarterline

An alias of [line](Line.md) where `linespacing` is 0.25.

## Syntax

````
|quarterline|
````

## Parameters

None.

## Remarks

See the [line](Line.md) page for more details on the function of the command as this one behaves the same way where `linespacing` is set to 0.25.

There is one issue specific to this command and [halfline](Halfline.md) however: In [Regular Letter Rendering](../Letter%20Rendering%20Methods/Regular%20Letter%20Rendering.md) and [Dialogue mode](../Dialogue%20mode.md), this command is accumulated for the [Backtracking](../Related%20Systems/Backtracking.md) system twice instead of only once. This is because being in [Dialogue mode](../Dialogue%20mode.md) no matter the letter rendering method will accumulate it before processing the command in the [Vertical bar processing](../Life%20Cycle.md#vertical-bar-processing), but since this command is also an alias of [line](Line.md), it will also accumulate it a second time if the rendering method is [Regular Letter Rendering](../Letter%20Rendering%20Methods/Regular%20Letter%20Rendering.md) which makes it repeat twice in a row when backtracking to the textbox containing the command.

This commend is treated like a manual line break by [OrganiseLines](../Related%20Systems/Automatic%20Line%20Breaks/OrganiseLines.md) since it logically resets the line to the first one and goes at the start of it.

This is one of the command supported by [testdiag](Testdiag.md) if the command name part is written as `line` with case sensitivity.
