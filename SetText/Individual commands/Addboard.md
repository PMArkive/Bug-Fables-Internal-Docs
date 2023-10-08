# Addboard

Add a [BoardQuest](../../Enums%20and%20IDs/BoardQuests.md) to the open board.

## Syntax

````
|addboard,quest|
````

## Parameters

### `quest`: int | string

The [BoardQuest](../../Enums%20and%20IDs/BoardQuests.md) id or its enum value's string representation to add to the open board. The int form must be a valid [BoardQuest](../../Enums%20and%20IDs/BoardQuests.md) and the string form must be a value [BoardQuest](../../Enums%20and%20IDs/BoardQuests.md) enum value or an exception will be thrown.

## Remarks

This command is unused under normal gameplay, but remains functional.
