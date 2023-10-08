# Line

Go to a new line below and continue rendering at the start of the new line

## Syntax

(1)

````
|line|
````

(2)

````
|line,linespacing|
````

## Parameters

### `linespacing`:  float

A factor to multiply the standard line height to move below the current one. This value must be a valid float or an exception will be thrown. The default value is 1.0 (which will use the standard line spacing).

## Remarks

This command functions slightly differently than the regular LF processing in [Dialogue mode](../Dialogue%20mode.md) when the language is set to `Japanese`. When an LF gets processed in Japanese, it acts like this command with `linespacing` set to 1.25. In other cases, it behaves like syntax (1).

In [Regular Letter Rendering](../Letter%20Rendering%20Methods/Regular%20Letter%20Rendering.md), this command is accumulated for the [Backtracking](../Related%20Systems/Backtracking.md) system.

This commend is treated like a manual line break by [OrganiseLines](../Related%20Systems/Automatic%20Line%20Breaks/OrganiseLines.md) since it logically resets the line to the first one and goes at the start of it.

This is one of the command supported by [testdiag](Testdiag.md) if the command name part is written as `line` with case sensitivity.
