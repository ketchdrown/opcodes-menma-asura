# Opcodes | Definitions for private servers
Full list of opcodes / definitions for patch v92.4 and v100.2

## This is a data-type repo, not a working script itself
Feel free to contibute with your data! -> open pull request

### How to update any script manual
* Open index.js (edit it via any text editor VS code studio pref)
* Search for definitions: (example)

dispatch.hook('C_PLAYER_LOCATION', <b>6</b>, (event) => .....

* Check out [DEFINITIONS PATCH v100.2](https://github.com/ketchdrown/data-private-servers/tree/main/v100.02/definitions)
* Check if the value after definition inside the code equeals the one that is working for x game patch
* If it is not change the value (in this case it will equals "5"

dispatch.hook('C_PLAYER_LOCATION', <b>5</b>, (event) => .....

* Example no.2
* What if there is something more then a number?
 
dispatch.hook('S_PLAYER_STAT_UPDATE', <b>dispatch.majorPatchVersion >= 109 ? 16 : 15</b>, (event) => ....

* Delete "dispatch.majorPatchVersion >= 109 ? 16 : 15"
* Type pure number (in this case = 14 for patch 100.2)

dispatch.hook('S_PLAYER_STAT_UPDATE', <b>14</b>, (event) => ....

* Save the file -> disable autoupdate so your edits won't change

## FOR DEVELOPERS

TERA's network data follows a custom protocol. It is convenient to describe the
order and meaning of each element in a "packet", which is done through a `.def`
file under the `protocol` directory, and named after the opcode it belongs to.

Each line in the `.def` must consist of the following, in order:
- An optional series of `-` for array and object definitions. These may be
  separated by spaces. To nest arrays or objects, just add another `-` to the
  front.
- A field type. Valid types listed below.
- At least one space.
- The name of the field.

A `#` and anything after it on the line are treated as comments and will be
ignored when parsing.

The formal grammar for this syntax is in the appendix at the end of this
document.

The following simple field types are supported:
- `bool`: A single byte that equals `true` for any non-zero value (and
  optionally warns for any value above 1).
- `byte`
- `float`: A four-byte floating-point number.
- `double`
- `int16`
- `int32`
- `int64`
- `uint16`
- `uint32`
- `uint64`

The following complex field types are supported:
- `vec3` - 3D vector used for location. See [tera-vec3](https://github.com/caali-hackerman/tera-vec3).
- `vec3fa` - Rotation Vec3 used for accessory transforms.
- `skillid` - An abstract representation of packed skill IDs. Contains the following properties and methods:
- - `npc` (Boolean) Indicates an NPC skill
- - `type` (Number) 1 = Action, 2 = Reaction (CC, pull, etc.)
- - `huntingZoneId` (Number) secondary key for type 1 NPC skills
- - `id` (Number) Skill ID
- - `reserved` (Number) Reserved bits, typically unused
- - `equals(skillId)`
- - `clone()`
- - `toString()`

There is one type that is not directly represented by the raw data and instead
serves organizational purposes:
- `object`: Any fields under this one should be collected into some sort of
  encapsulating object. For instance, location, angle, and character id for a
  targeted enemy can be under an `object` called "target".

There are also a few variable-length fields:
- `array`: Almost like `object`, except there can be 0 or more that should be
  collected into an array.
- `bytes`: A series of `byte` data.
- `string`: String data, encoded as null-terminated UTF-16LE (in other words, a
  series of `uint16` where the final value is 0).

Each of the variable-length fields has accompanying metadata. These fields are
determined implicitly (see the section below for the generation rules), but in
some cases, particularly with legacy definitions, the following metatypes can
be used. **These types are deprecated, and use of them will disable implicit
handling of these types:**
- `count`: Acts as `uint16`. Dictates the length of an `array` or `bytes` field
  of the same name.
- `offset`: Acts as `uint16`. Indicates the byte offset from the beginning of
  the message for an `array`, `bytes`, or `string` field of the same name.

More details on the original message format are below, while details on your
language's or library's implementation of these types should be described in
your library's documentation.

### Message Format

TERA's networking encodes all data in little-endian.

There are a few fields which are implied because they are never omitted. Every
packet begins with two fields:
- `uint16 length`, which describes the byte length of the message, including
  this header.
- `uint16 opcode`, which describes which kind of message this is. By looking up
  which name has this number in the mapping, you will know what the message is
  called.

Following these two fields are the metatypes for all variable length fields. To
produce the list, iterate through all variable length fields in order, and
depending on the type, add a `count` and/or `offset` field for it:
- `array`: Add `count` then `offset`.
- `bytes`: Add `offset` then `count`.
- `string`: Add `offset` only.

Additionally, all array elements begin with two fields:
- `offset here`, which can be used to verify correctness. If this is the first
  element, the `offset` for the array should match this; otherwise, it should
  match the `next` for the previous element.
- `offset next`, which points to the byte offset of the next element in the
  array, or zero if this is the final element.

In general, you will find `count` and `offset` fields at the beginning of a
message or array definition, and their corresponding fields at the end.

#### Example

Given the definition:

    int32 number
    array list
    - int16 value

A message will be parsed as if it were:

    uint16 (length)
    uint16 (opcode)
    count  list
    offset list
    int32  number
    array  list
    - offset (here)
    - offset (next)
    - int16  value

## Versioning

Protocol definitions contain version information in the filename:
`<NAME>.<VERSION>.def` where `<NAME>` is an opcode name and `<VERSION>` is an
integer starting from 1 and incrementing with each change.

**When submitting changes, contributors _must_ leave older versions untouched
unless they are trivially backwards compatible. Instead, submit the changed
definition as a new file with the version number incremented.**

Whenever TERA receives a major patch, a tag will be added to the repository,
and then the mappings will be updated and all outdated definition files will be
deleted.

## Contributing

* [Calli](https://github.com/caali-hackerman)
* [TeraProxy](https://github.com/TeraProxy)
* [Salty](https://github.com/SaltyMonkey)
* [Pinkie](https://github.com/pinkipi)
