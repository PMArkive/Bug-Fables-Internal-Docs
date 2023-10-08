# Checkinvqtd

Check that the quantity of [items](../../Enums%20and%20IDs/Items.md) in an inventory is equal or higher than a specific quantity or the maximum amount allowed by the game for this specific inventory. If it is, replaces the input string by another dialogue line.

## Syntax

````
|checkinvqtd,invtype,quantity,dialogue|
````

## Parameters

### `invtype`: `0` | `1` | `2`

The inventory type to check the quantity:

* 0: Standard items
* 1: Key items (this will cause the command to do nothing). 
* 2: Storage items.

Any other value will cause an exception to be thrown.

### `quantity`: `full` | int

The quantity to compare against. If it is `full`, the quantity depends on `invtype`:

* 0: The current amount of maximum standard items allowed in inventory.
* 1: N/A (will never report to exceed the quantity)
* 2: The amount of maximum storage items allowed (this is always 35 under normal gameplay).

If it is an int, this refers to a specific quantity and it must corresponds to a valid int value or an exception will be thrown.

### `dialogue`: int

The [Dialogue line id](../Common%20commands%20id%20schemes/Dialogue%20line%20id.md) to redirect if the quantity is equal or higher than `quantity`. This value must be a valid [Dialogue line id](../Common%20commands%20id%20schemes/Dialogue%20line%20id.md) or an exception will be thrown.

## Remarks

Whenever the input string is replaced, it is done with an [OrganiseLines](../Related%20Systems/Automatic%20Line%20Breaks/OrganiseLines.md) version of dialogue string prepended with |[blank](Blank.md)\|.

If the input string is replaced, this command will resume processing at the start of the new input string.
