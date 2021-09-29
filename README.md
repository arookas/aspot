
# aspot v1.0

## Description

This mod is a universal ping/spot mod for [GZDOOM](https://zdoom.org/index) designed with cooperative play in mind.
It adds a single action to mark a spot or thing under the crosshair, visible to all players.

## Credits

This mod uses a modified [_LibEye_](https://forum.zdoom.org/viewtopic.php?f=105&t=64566) library by _KeksDose_;
the notice has been included [here](./zscript/arookas/spot/projection.txt).
For all other code, see the [LICENSE](./LICENSE.md) file.

Take a moment to appreciate these beautiful people:

| **Greyfalll** | **Accensus** &bullet; **phantombeta** | **KeksDose** |
|:-:|:-:|:-:|
| support / testing | programming assistance | _LibEye_ library |

This mod uses the following resources:

| Resource | Authors |
|---------:|:-------|
| `ASPOT**.png` | arookas |
| `ARNG**.png` | arookas |
| `spot_ring.wav` | Sandlot |

## Usage

You can download **aspot** by going to the [Releases](https://github.com/arookas/aspot/releases) page, or by clicking the green **Code** button above and clicking **Download ZIP**.
The mod is designed to be universal, so simply load it alongside your other mods to begin using it.

### Controls

The spot command is `arookas_spot` and can be found as the "_Spot_" control under the _Multiplayer Controls_ menu.
Bind it to any input of choice.

### Behavior

When spotting, an indicator will appear at the point under that player's crosshair.
It will be styled according to what was targetted, and will automatically follow moving actors.
Each player can have only one active spot at a time; activating the command again will remove any existing indicators.

The types of indicators are as follows:

| Target | Indicator † | `ASpotType` value |
|-------:|:----------|-------------------|
| Living monster | Red | `ASPOT_MONSTER` |
| +SHOOTABLE actor | Red | `ASPOT_MONSTER` |
| Inventory item | Green | `ASPOT_PICKUP` |
| +COUNTITEM actor | Green | `ASPOT_PICKUP` |
| Other player | Blue | `ASPOT_FRIEND` |
| +FRIENDLY actor | Blue | `ASPOT_FRIEND` |
| Dead monster | White | `ASPOT_WORLD ` |
| Regular actor | White | `ASPOT_WORLD` |
| Level geometry | White | `ASPOT_WORLD` |

> **†** Colors are based on the builtin "ring" style.
> Other styles may use different colors.

### Customization

The indicators can be configured using the `aspot_style` CVAR.
**aspot** provides the following builtin styles:

| Name |   | Origin | Description |
|-----:|:-:|--------|-------------|
| `ring` | (default) | _Earth Defense Force_ | Simple animated ring indicator with color coding and sound. |
| `icon` | | _Risk of Rain 2_ | More advanced indicator with icons, color coding, name tags, and console logging. |

More styles can be added as submods by implementing the ZScript `ASpotStyle` interface.
The interface has the following functions:

| Function | Scope | Description |
|---------:|-------|-------------|
| `GetStyleName` | `data` | Returns the name of the style. Used by the `aspot_style` CVAR to identify the style. The default behavior returns the class name. |
| `LoadStyle` | `data` | Used to initialize any resources and data used by the style. Returns `true` if successfully loaded and `false` upon error. |
| `SpawnSpot` | `play` | Spawns the 3D actor used to position the indicator. The actor returned by this function controls the indicator duration and sounds. |
| `DrawSpot` | `ui` | Draws the 2D indicator on the player's screen. This function controls the indicator appearance. |

For simple styles that use a 2D texture and sound, you can use the `ASpotBasicStyle` class instead.
This class exposes a new interface and offloads most of the boilerplate:

| Function | Scope | Description |
|---------:|-------|-------------|
| `GetTexName` | `data` | Specifies the texture to use for a given `ASpotType`. See [Behavior](#Behavior) for the full list of spot types. |
| `GetSpotClass` | `data` | The class of the spot actor. Defaults to `ASpotBasicPuff`, which lasts for 8 seconds. |
| `GetSpotSound` | `data` | Override this to add a sound to the spot. By default, no sound will be played. This should be the logical name as specified in SNDINFO. |
| `GetDrawMargin` | `data` | Optional `vector2` margin on the edges of the screen for the 2D indicator texture. The graphic will clamp to this border. Units are same as in `Screen`. |

> **Note:** See the [builtin "ring" style](./zscript/arookas/spot/styles/ring.txt) for an example implementation.

Once a custom style is defined, load the mod alongside **aspot** and set your `aspot_style` CVAR to the name of the custom style.
If the chosen style cannot be found (e.g. its containing mod is no longer loaded), or an error occurs when initializing the style, the style will default to the builtin "ring" style.
