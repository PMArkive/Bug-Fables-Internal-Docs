# Entity data

Entity data are split into 2 TextAssets in the game loaded on boot:

* `Resources/data/EntityValues`
* The `Resources/data/entitydata` directory

## [animid](../Enums%20and%20IDs/AnimIDs.md) data

The TextAsset `Resources/data/EntityValues` from the root of the asset tree contains data that apply to every entities of a given [AnimID](../Enums%20and%20IDs/AnimIDs.md). Each line id of the data corresponds to the matching [AnimID](../Enums%20and%20IDs/AnimIDs.md). The data is loaded on boot during LoadEssentials in the `endata` static field of MainManager. The field is an array of struct of type `Entity_Data`. Specifically, it's done by a method called by LoadEssentials: LoadEntityData:

```cs
public static Entity_Data[] LoadEntityData()
```

NOTE: This asset uses the enum form of the [AnimIDs](../Enums%20and%20IDs/AnimIDs.md), NOT the int form which means the first line represents `None`.

### A word on the asset's format

This format is very unique in the game: it is not encoded in a similar fashion than other TextAssets in the game. Here is how the data is serialiazed:

* The fields are split by `,`
* Every primitives types are encoded as normal (int, float, string, boolean, etc...)
* A `Vector3` is NOT encoded using 3 fields. It is encoded using 1 field with the format `(x, y, z)` where `x`, `y` and `z` are the floats of the vector. Note that the inner separators are expected: the parser will expect the parenthesizes delimiters to know this is parsed differently.
* An empty string is an empty field.

### TextAsset and `Entity_Data` layout

Here is the layout of the data and the fields associated with this struct (NOTE: the position is given according to the TextAsset which means `Vector3` are treated as one position):

|Position|Description|Type|Additional information|
|--------:|-----------|----|----------------------|
|0|shadowsize|float|The default value is 1.0 if the [AnimIDs](../Enums%20and%20IDs/AnimIDs.md) is higher than the max line id contained in the asset|
|1|startscale|Vector3|The default value is Vector3.one if the [AnimIDs](../Enums%20and%20IDs/AnimIDs.md) is higher than the max line id contained in the asset|
|2|bleeppitch|float|The default value is 1.0 if the [AnimIDs](../Enums%20and%20IDs/AnimIDs.md) is higher than the max line id contained in the asset. See [bleep](../SetText/Individual%20commands/Bleep.md) to learn more|
|3|bleepid|int|See [bleep](../SetText/Individual%20commands/Bleep.md) to learn more|
|4|ismodel|bool|Check the section below about model animids to learn more|
|5|modelscale|Vector3|For an ismodel animid, this represents the euler angles the `model` will have. See the section below about model animids to learn more|
|6|modeloffset|Vector3|For an ismodel or Object animid, the offset to use when calling [AddModel](../Entities/EntityControl/Notable%20methods/AddModel.md#addmodel). See the section below about model animids to learn more|
|7|freezesize|Vector3||
|8|freezeoffset|Vector3||
|9|freezeflipoffset|Vector3||
|10|preloaddata|string\[\]|The strings are split by `?`, can be left empty in which case, the array is left to `null`. The strings represents asset paths relative from the Asset directory. A `$` prefix means to not preload outside of battle while a `&` indicate it is a sprite instead of a game object from a prefab|
|11|shakeondrop|bool||
|12|diganim|bool||
|13|dontoverridejump|bool||
|14|freezenofall|bool||
|15|noshadow|bool||
|16|walktype|WalkType|Encoded in its int form, see below for the possible values|
|17|basestate|int|This field is loaded, but not used. A value must still be provided or an exception will be thrown.|
|18|basewalk|int|This field is loaded, but not used. A value must still be provided or an exception will be thrown.|
|19|minheight|float||
|20|startheight|float|This field is loaded, but not used. A value must still be provided or an exception will be thrown.|
|21|startbobspd|float||
|22|startbobfreq|float||
|23|hasiceanim|bool||
|24|noflyanim|bool|This field is optional, it can be omitted which leaves a default value of false|
|25|forceshadow|bool|This field is optional, it can be omitted which leaves a default value of false|
|26|Object|bool|Check the section below about model animids to learn more. This field is optional, it can be omitted which leaves a default value of false|

NOTE: Fields 24, 25 and 26 must be omitted TOGETHER if any are to be omitted. Failure to do so will result in an exception to be thrown when parsing the asset.

### WalkType values
Here are the possible values for a WalkType enum value:

|Value|Name|
|-----:|----|
|0|Normal|
|1|Jump|

The only thing this field does is if it's `Jump`, [Move](../Entities/EntityControl/Notable%20methods/Move.md) will have the entity continuously jump when it can during the movement.

### ismodel and Object animids
These fields, when set to true, acomplishes a similar goal, but executed differently. At most, one of these 2 fields can be true, it is considered invalid for both of them to be true at once (the `WindUp` animid has both fields set to true, but this animid is unused under normal gameplay).

#### What these fields have in common
Either of them being true means that every entities created using this animid doesn't follow the regular structure that most entities follow. The regular structure is normally enforced by [CreateNewEntity](../Entities/EntityControl/EntityControl%20Creation.md) where they have a SpriteRenderer that gets animated using an Animator present on the entity's root and whose controller comes from loading a RuntimeAnimatorController present in the resources tree at `animationcontrollers/X/X` where `X` is the enum value name of the animid (see the [animids](../Enums%20and%20IDs/AnimIDs.md) table for details).

Instead of having this structure, [AddModel](../Entities/EntityControl/Notable%20methods/AddModel.md) is called during [CheckSpecialID](../Entities/EntityControl/Notable%20methods/CheckSpecialID.md), a part of the entity startup process. AddModel will change the meaning of `sprite` to instead be the root of an instance of a prefab that was loaded from the resources tree at `prefabs/objects/X` where `X` is the animid enum value name. This occurs after the specific animid logic have run (though some specific animid have logic that occurs after, but they aren't common). As for the animations, the `anim` will be set to the animator that can be present in in the object prefab's root (the controller can either come from directly referencing it in the prefab's animator or it can be loaded from the resources tree as normal if it's not assigned). However, this animator is optional: it's possible to have a model animid that cannot be animated and will have its `anim` left at null. This allows entity with a custom object hierarchy that are more complex than a sprite to be animated with their custom controller or to simply not have any animations done on them.

#### Where these fields differ
Setting either ismodel or Object to true will result in this model structure. Where they differ is how the animid is expected to be animated throughout the entity's lifetime. The animation system EntityControl provides and the [animstate](../Entities/EntityControl/Animations/animstate.md) system can still be used if an Animator is present on the root prefab. Where there's a potential problem is when an animator isn't present. In that case, EntityControl would still execute logic that tends to get in the way because such an animid isn't intended to be animated.

This problem is where the nuance between ismodel and Object comes in. ismodel ONLY makes the entity a model structure, but it is expected to have an animator at its root with a controller (either loaded from the prefab's animator or from resources). Object expects to not have an animator present on the prefab and goes one step further: after AddModel is done, the entity's `animid` field will be set to -1.

Normally, this would result in the entity not being rendered, but because [UpdateSprite](../Entities/EntityControl/Update%20process/UpdateSprite.md) doesn't actually listen for `animid` changes to model entities, this doesn't happen. What happens instead is it will block a significant amount of logic that UpdateSprite would have done instead. In fact, it completely disables any automatic management of the animations and `animstate` so it is no longer possible with an Object animid to have EntityControl manage the animations. It's not even possible to use an Animator that would be present on the object prefab's root because UpdateSprite would set it to null. Since it's expected for the entity to not have animations, this is well suited for this.

> NOTE: This implies that in order to properly know the actual animid of Object entities, the `originalid` field needs to be used instead of `animid`.

#### A way for Object animids to be animated
However, there is a narrow exception where EntityControl allows an Object animid to have an animator with its own controller. If the entity is backed by an [NPCControl](../Entities/NPCControl/NPCControl.md), UpdateSprite will not set the `anim`'s controller to null. This specifically allows those entities to still have an animator at the root of their prefab without EntityControl setting it to null due to the `animid` being -1. However, it ONLY allows `anim` to not be null: the controller MUST be provided by the prefab (they will not be automatically loaded through the resources tree) and the animation clips still cannot be automatically managed by EntityControl. The only way to play clips on such animator is to do so manually through code. It is effectively only allowing [non standard animation](../Entities/EntityControl/Animations/animstate.md#non-standard-animations) to work.

It should also be noted that an Object animid that is used by an [Enemy](Enemies%20data.md) won't actually have their `animid` set to -1 and thus, it behaves more like a ismodel entity (with the exception of the additional logic mentioned in the next section). This happens because while CheckSpecialID will set the `animid` to -1, this will be undone by [GetEnemyData](Enemies%20data.md#getenemydata) when called with createnewentity since it will itself set the `animid` to the one from the enemy data. Since this happens after the entity was created, it overrides the main effect of the Object field, but it preserves the model structure. Under normal gameplay, this only happens with the `CoiledVineGround` animid used in the `AcolyteVine` enemy (which is actionless and only involved in the [Acolyte](../Battle%20system/Enemy%20actions/Enemies/Acolyte.md) enemy).

#### Other specific logic
There are other effects an Object animid have enforced by CheckSpecialID:

- `notalk` is set to true
- `hasshadow` is set to false
- [UpdateAnimSpecific](../SetText/Individual%20commands/Updateanim.md) will not be called upon starting the entity

ismodel on the other hand doesn't do the above, but it does have one exclusive change: modelscale will be used to set the euler angles of the `model`. The field is not used for Object entities.

#### In summary
Here are criteria to choose when an animid should be ismodel or Object:

- If EntityControl should manage the animations of every entities with this animid, it should be ismodel. An animator should be present on the object prefab's root and either directly have a controller assigned or load one from the resources tree like a regular animid. The modelscale will allow to configure the euler angles of the `model`
- If there's no need for any animations of every entities with this animid, it should be Object (modelscale won't be used)
- If the entity is meant to be used in an NPCControl with Unity animation clips, but there's no need for those clips to be automatically managed by EntityControl, it should be Object (EntityControl will still allow an animator to exist on the object prefab's root, but it won't manage it)

## Map entity data
The `entitydata` directory contains the names and details that applies to each specific entities that should be loaded upon a specific [Map](../Enums%20and%20IDs/Maps.md) load. The data only gets loaded during MapControl's [CreateEntities](../MapControl/Init%20methods/CreateEntities.md) for the concerned [Maps](../Enums%20and%20IDs/Maps.md) which happens on the MapControl's Start. The data ends up in the `entities` field of the MapControl which is an array of EntityControl and each index of that array ends up in the npcdata.`mapid` field of each [NPCControl](../Entities/NPCControl/NPCControl.md). The loaded data ends up in `endata` and it is possible to retrieve the fields later. This is how [CheckSpecialID](../Entities/EntityControl/Notable%20methods/CheckSpecialID.md) reads some of them.

Each line of an entity map asset contains one line per entity. It contains fields about its [EntityControl](../Entities/EntityControl/EntityControl.md) and its [NPCControl](../Entities/NPCControl/NPCControl.md) (via the `npcdata` field) separated by `}`. Each asset corresponds to each map in the game where its asset's filename is the [Map](../Enums%20and%20IDs/Maps.md)'s id.

The names of each entity are not contained in the data assets. They are contained in a `names` directory next to the data assets. An entity name asset contains one string per line that corresponds to the name of entity of the matching line in the matching data asset. The names asset filename is `Xnames` where X is the [Map](../Enums%20and%20IDs/Maps.md) id.

This gives the following layout:

* Path to the entity data files: `Resources/data/entitydata`
* Path to the entity name files: `Resources/data/entitydata/names`

The name of an entity can contain special strings to act as [modifiers](../Entities/EntityControl/Modifiers.md) on it which means the name is both used for debugging purposes as well as to modify its behavior.

### Entity Data File Fields
While a lot of these fields have shared meanings, a lot are dependant of the specifics of the [NPCControl](../Entities/NPCControl/NPCControl.md) (for example, the `data` fields completely changes between them). It is recommended to check the NPCControl documentation to understand where each field is used in what circumstances. The [EntityControl](../Entities/EntityControl/EntityControl.md) fields however tends to remain shared.

|Position|Description|Type|Additional information|
|--------:|-----------|----|----------------------|
|0|npcdata.[entitytype](../Entities/NPCControl/NPCType.md)|NPCControl.NPCType||
|1|npcdata.[objecttype](../Entities/NPCControl/Object.md#objecttypes)|NPCControl.ObjectTypes||
|2|npcdata.[behaviors](../Entities/NPCControl/ActionBehaviors.md)\[0\]|NPCControl.ActionBehaviors||
|3|npcdata.[behaviors](../Entities/NPCControl/ActionBehaviors.md)\[1\]|NPCControl.ActionBehaviors||
|4|npcdata.[interacttype](../Entities/NPCControl/Interaction.md)|NPCControl.Interaction|If it's [Shop](../Entities/NPCControl/Interaction/Shop.md), this tells the game to build a [shop system](../Entities/NPCControl/Shop%20system.md)|
|5|destroytype|NPCControl.DeathType|Used in [Death](../Entities/EntityControl/Notable%20methods/Death.md) to determine how to destroy the entity|
|6|startpos.x|float||
|7|startpos.y|float||
|8|startpos.z|float||
|9|[animid](../Enums%20and%20IDs/AnimIDs.md)|int|This is the item type for an [Item](../Entities/NPCControl/ObjectTypes/Item.md) object|
|10|flip|bool||
|11|ccol.height|float||
|12|ccol.radius|float|Also assigned to npcdata.colliderheight|
|13|npcdata.radius|float|Regulates how close the player needs to be to become `inrange` which affects behaviors and interactions|
|14|npcdata.timer|float|For a lot of objects, this is a timer in frame that when expired, [Death](../Entities/EntityControl/Notable%20methods/Death.md) is called on the entity. -1 means to not have a timer|
|15|speed|float||
|16|npcdata.actionfrequency\[0\]|float||
|17|npcdata.actionfrequency\[1\]|float||
|18|npcdata.speedmultiplier|float||
|19|npcdata.radiuslimit|float|The radius the entity can be in and will not go outside of it in multiple cases|
|20|npcdata.wanderradius|float|For a [Wander](../Entities/NPCControl/ActionBehaviors/Wander.md) behavior or derivative, this is the radius the entity can move to between wanders|
|21|npcdata.teleportradius|float|For a [Wander](../Entities/NPCControl/ActionBehaviors/Wander.md) behavior or derivative, this is the radius the entity can teleport to in the case of a failsafe situation|
|22|NPCControl has a BoxCollider?|bool||
|23|npcdata.boxcol.isTrigger|bool|Requires field at position 22 to be true|
|24|npcdata.boxcol.size.x|float|Requires field at position 22 to be true|
|25|npcdata.boxcol.size.y|float|Requires field at position 22 to be true|
|26|npcdata.boxcol.size.z|float|Requires field at position 22 to be true|
|27|npcdata.boxcol.center.x|float|Requires field at position 22 to be true|
|28|npcdata.boxcol.center.y|float|Requires field at position 22 to be true|
|29|npcdata.boxcol.center.z|float|Requires field at position 22 to be true|
|30|npcdata.freezetime|float||
|31|freezesize.x|float||
|32|freezesize.y|float||
|33|freezesize.z|float||
|34|freezeoffset.x|float||
|35|freezeoffset.y|float||
|36|freezeoffset.z|float||
|37|npcdata.eventid|int||
|38|Length of npcdata.requires|int|Maximum of 10|
|39-48|npcdata.requires|int\[\]|lists of [flags](../Flags%20arrays/flags.md) slots that needs to be true for the entity to exist|
|49|Length of npcdata.limit|int|Maximum of 10|
|50-59|npcdata.limit|int\[\]|lists of [flags](../Flags%20arrays/flags.md) slots where any needs to be false for the entity to exist|
|60|Length of npcdata.data|int|Maximum of 10|
|61-70|npcdata.data|int\[\]|General purpose int array|
|71|Length of npcdata.vectordata|int|Maximum of 10|
|72-101|npcdata.vectordata|Vector3\[\] (each component is a field)|General purpose triplets of floats array|
|102|Length of npcdata.dialogues|int|Maximum of 20|
|103-162|npcdata.dialogues|Vector3\[\] (each component is a field)|For an [NPC](../Entities/NPCControl/NPC.md), consult the documentation of [GetDialogue](../Entities/NPCControl/Notable%20methods/GetDialogue.md) to learn how this array is managed|
|163|transform.eulerAngles.x|float||
|164|transform.eulerAngles.y|float||
|165|transform.eulerAngles.z|float||
|166|Length of npcdata.battleids|int|Maximum of 4|
|167-170|npcdata.battleids|int\[\]|For an [Enemy](../Entities/NPCControl/Enemy.md), this is the list of [enemy id](../Enums%20and%20IDs/Enemies.md) the encounter consists of|
|171|npcdata.tagcolor.r|float|Doesn't affect gameplay, only used in the Unity editor|
|172|npcdata.tagcolor.g|float|Doesn't affect gameplay, only used in the Unity editor|
|173|npcdata.tagcolor.b|float|Doesn't affect gameplay, only used in the Unity editor|
|174|npcdata.tagcolor.a|float|Doesn't affect gameplay, only used in the Unity editor|
|175|emoticonoffset.x|float||
|176|emoticonoffset.y|float||
|177|emoticonoffset.z|float||
|178|npcdata.insideid|int|The interior id of the map the entity starts in|
|179-188|npcdata.emoticonflag|Vector2\[10\] (components are `,` separated)|See [CheckEmoteFlag](../Entities/NPCControl/Notable%20methods/CheckEmoteFlag.md)|
|189|npcdata.tattleid|int|The [dialogue line id](../SetText/Common%20commands%20id%20schemes/Dialogue%20line%20id.md) corresponding to tattling the NPCControl|
|190|npcdata.regionalflag|int|The [Regionalflags](../Flags%20arrays/Regionalflags.md) slot bound to this entity|
|191|initialheight|float||
|192|bobrange|float||
|193|bobspeed|float||
|194|npcdata.activationflag|int|The [flag](../Flags%20arrays/flags.md) slot bound to this entity|
|195|npcdata.returntoheight|bool or int|Int if the length in string is 1, bool otherwise|
