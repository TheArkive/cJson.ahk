
# cJson.ahk

The first and only AutoHotkey JSON library to use embedded compiled C for high
performance.

## Compatibility

This library is compatible *ONLY* with AutoHotkey v1.1 U64.

32-bit support is dependent on 32-bit support of the underlying MCLib
dependency, which currently has no plan to support 32-bit code generation.

AHKv2 compatibility, especially as v2 continues to evolve, will require
modification to both the AHK wrapper and the C implementation as the language
maintainer makes changes to internal Object structs. Support is planned, but
may not be implemented any time soon.

## Using cJson

Converting an AHK Object to JSON:

```ahk
#Include <cJson>

; Create an object with every supported data type
obj := ["abc", 123, {"true": true, "false": false, "null": ""}, [cJson.true, cJson.false, cJson.null]]

; Convert to JSON
MsgBox, % cJson.Dumps(obj) ; Expect: ["abc", 123, {"false": 0, "null": "", "true": 1}, [true, false, null]]
```

Converting JSON to an AHK Object:

```ahk
#Include <cJson>

; Create some JSON
json = ["abc", 123, {"true": 1, "false": 0, "null": ""}, [true, false, null]]
obj := cJson.Loads(json)

MsgBox, % obj[1] ; abc
MsgBox, % obj[2] ; 123

MsgBox, % obj[3].true ; 1
MsgBox, % obj[3].false ; 0
MsgBox, % obj[3].null ; *nothing*

MsgBox, % obj[4, 1] == cJson.True ; 1
MsgBox, % obj[4, 2] == cJson.False ; 1
MsgBox, % obj[4, 3] == cJson.Null ; 1
```

## Notes

### Data Types

AutoHotkey does not provide types that uniquely identify all the possible values
that may be encoded or decoded. To work around this problem, cJson provides
magic objects that give you greater control over how things are encoded. By
default, cJson will behave according to the following table:

| Value         | Encodes as | Decodes as    |
|---------------|------------|---------------|
| `true`        | `1`        | `cJson.True`  |
| `false`       | `0`        | `cJson.False` |
| `null`        | N/A        | `cJson.Null`  |
| Floats        | String     | Float/String  |
| `cJson.True`  | `true`     | N/A           |
| `cJson.False` | `false`    | N/A           |
| `cJson.Null`  | `null`     | N/A           |

### Array Detection

AutoHotkey makes no internal distinction between indexed-sequential arrays and
keyed objects. As a result, this distinction must be chosen heuristically by the
cJson library. If an object contains only sequential integer keys starting at
`1`, it will be rendered as an array. Otherwise, it will be rendered as an
object.

## Roadmap

* Add a switch to prefer AHK true/false over cJson true/false when rehydrating.
* Add a simple way to completely override the value rehydrator.
* Add a special class to force encoding of floating-point strings as floats.
* Add a special class to force encoding of indexed arrays as objects.
* Integrate with a future MCLib-hosted COM-based hash-table style object for
  even greater performance.

---

## [Download cJson.ahk](https://github.com/G33kDude/cJson.ahk/releases)