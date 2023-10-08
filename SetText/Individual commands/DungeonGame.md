# DungeonGame

Start a game of Mite Knight (aka MazeGame) and yield until it is over.

## Syntax

````
|dungeongame|
````

## Parameters

None.

## Remarks

The startup process is as follow:

* Set the current textbox to hide with a shrink animation as well as a fade to black with a speed of 0.1 then yield for 1 second
* Save the values of the current music, the fog color and the camera limit
* Effectively remove any camera limit by setting the bounds from (-999.0, -999.0, -999.0) to (999.0, 999.0, 999.0)
* Set [flagvar](../../Flags%20arrays/flagvar.md) 6 to 1 (this is needed for the game later to know what game was completed if it was to properly reward the tokens amount)
* Set the position of the game's transform to (0.0, 70.0, 0.0) and the camera speed to 1.0 (instant)
* Yield for a frame
* Set the camera speed to 0.1 (the default speed)
* Yield for a frame
* Yield until the MazeGame's object goes to null (this is done some time after its destruction)

Once the game is over:

* The camera limits are restored and the camera is reset instantaneously.
* The fog color and render distance are restored
* The music is changed to the one before the game started
* A fade out to reveal the main game is done
* Yield for a second
* Disable the [Text advance](../Related%20Systems/Text%20advance.md)'s skiptext
* Reveal the textbox with a grow animation
* Reassign the blinker

It should be noted that the game will write to [flagvar](../../Flags%20arrays/flagvar.md) 0 with the score during the process of ending the mini game.

As for the camera change, this turns out to be the cause of a quirk because while the game initially was intended to move the camera instantaneously, it needs to use the default speed after it is fully initialized which is what SetText is doing. However, it does so too early so it affects the game's start which makes the camera slowly move towards its starting position on the first floor.
