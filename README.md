
# aspot v1.0

## Description

This mod is a universal ping/spot mod for [GZDOOM](https://zdoom.org/index) designed with cooperative play in mind.
It adds a single action to mark a spot or thing under the crosshair, visible to all players.

## Credits

This mod uses a modified [_LibEye_](https://forum.zdoom.org/viewtopic.php?f=105&t=64566) library by _KeksDose_;
the notice has been included [here](./zscript/arookas/spot/projection.txt).
For all other code, see the [LICENSE](./LICENSE.md) file.

Take a look at these wonderful people:

| **Greyfalll** | **Accensus** &bullet; **phantombeta** | **KeksDose** |
|:-:|:-:|:-:|
| support / testing | programming assistance | _LibEye_ library |

This mod uses the following resources:

| Resource | Authors |
|---------:|:-------|
| `ASPOTA**.png` | arookas |
| `spot_ring.wav` | Sandlot |

## Usage

The mod is designed to be universal, so simply load it alongside your other mods to begin using it.

### Controls

The spot command is `arookas_spot` and can be found as the "_Spot_" control under the _Multiplayer Controls_ menu.
Bind it to any input of choice.

### Behavior

When spotting, an indicator will appear at the point under that player's crosshair.
It will be styled according to what was targetted, and will automatically follow moving actors.
Each player can have only one active spot at a time; activating the command again will remove any existing indicators.

The types of indicators are as follows:

| Target | Indicator | `ASpotType` value |
|-------:|:----------|-------------------|
| Living monster | Red | `ASPOT_MONSTER` |
| +SHOOTABLE actor | Red | `ASPOT_MONSTER` |
| Inventory item | Green | `ASPOT_PICKUP` |
| +COUNTITEM actor | Green | `ASPOT_PICKUP` |
| Other player | Blue | `ASPOT_FRIEND` |
| +FRIENDLY actor | Blue | `ASPOT_FRIEND` |
| Dead monster | White | `ASPOT_WORLD ` |
| Regular actor | White | `ASPOT_WORLD`     |
| Level geometry | White | `ASPOT_WORLD`     |

### Customization

The indicators can be configured using the `aspot_style` CVAR.
By default, this the builtin "ring" style.
More styles can be added as submods by implementing the ZScript `ASpotStyle` interface.

The interface has the following functions:

| Function | Scope | Description |
|---------:|-------|-------------|
| `GetStyleName` | `data` | Returns the name of the style. Used by the `aspot_style` CVAR to identify the style. The default behavior returns the class name. |
| `Load` | `data` | Used to initialize any resources and data used by the style. Returns `true` if successfully loaded and `false` upon error. |
| `SpawnSpot` | `play` | Spawns the 3D actor used to position the indicator. The actor returned by this function controls the indicator duration and sounds. |
| `DrawSpot` | `ui` | Draws the 2D indicator on the player's screen. This function controls the indicator appearance. |

> **Note:** See the [builtin style](./zscript/arookas/spot/styles/ring.txt) for an example implementation.

Once a custom style is defined, load the mod alongside **aspot** and set your `aspot_style` CVAR to the name of the custom style.
If the chosen style cannot be found (e.g. its containing mod is no longer loaded), or an error occurs when initializing the style, the style will default to the builtin "ring" style.
