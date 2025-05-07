# Buffer Library Documentation

**Author:** Madonox
**Year:** 2024

---

# Overview

The Buffer Library provides advanced mechanisms for serialization and deserialization of data into a binary buffer format. It supports both structured (templated) and manual (low-level) serialization. This system is particularly useful for networking, file storage, and custom data formats where compactness and efficiency are crucial.

# Components

* **Buffer**: Templated serialization/deserialization.
* **BufferInscriber**: Manual serialization/deserialization.
* **Utilities**: Helper functions for size calculation, type resolution, etc.

---

# Types

## BufferOptions

Configuration object for `Buffer` class instantiation.

```lua
--@type BufferOptions
{
  MakeBuffer: boolean,
  Template: {string},
}
```

## BufferModule

The core interface for templated serialization.

```lua
--@type BufferModule
{
  new: (BufferOptions) -> BufferModule,
  Write: (BufferModule, number, string, number) -> BufferModule,
  Read: (BufferModule, number, string) -> any,
  IncrementPointer: (BufferModule, number) -> BufferModule,
  Serialize: (BufferModule, {any}) -> buffer,
  Deserialize: (BufferModule) -> {any},
  ImportBuffer: (BufferModule, buffer) -> BufferModule,
  SetTemplate: (BufferModule, {string}) -> BufferModule,
  MakeNewBuffer: (BufferModule) -> BufferModule,
  Destroy: (BufferModule) -> nil,

  pointer: number,
  buffer: buffer,
  template: {[any]:any},
  __index: BufferModule,

  Types: {string:any},
}
```

## BufferInscriber

The core interface for manual, low-level buffer manipulation.

```lua
--@type BufferInscriber
{
  new: (buffer?) -> BufferInscriber,
  IncrementPointer: (BufferInscriber, number) -> BufferInscriber,
  SetPointer: (BufferInscriber, number) -> BufferInscriber,
  Write: (BufferInscriber, number, string, number) -> BufferInscriber,
  Read: (BufferInscriber, number, string) -> any,
  WriteType: (BufferInscriber, string, any) -> BufferInscriber,
  ReadType: (BufferInscriber, string, number?) -> (any, number),
  SetBuffer: (BufferInscriber, buffer?) -> BufferInscriber,
  Destroy: (BufferInscriber) -> nil,

  pointer: number,
  buffer: buffer,
  __index: BufferInscriber,
}
```

# Classes

## Buffer

Handles templated serialization and deserialization.

### Methods

* `Buffer.new(options: BufferOptions): BufferModule`
* `Buffer:Serialize(data: {any}): buffer`
* `Buffer:Deserialize(): {any}`
* `Buffer:Write(memorySize: number, dataType: string, value: number): BufferModule`
* `Buffer:Read(memorySize: number, dataType: string): any`
* `Buffer:IncrementPointer(amount: number): BufferModule`
* `Buffer:SetTemplate(template: {string}): BufferModule`
* `Buffer:MakeNewBuffer(): BufferModule`
* `Buffer:ImportBuffer(buffer: buffer): BufferModule`
* `Buffer:Destroy(): nil`

### Fields

* `pointer: number`
* `buffer: buffer`
* `template: {[any]: any}`
* `Types: {string: any}`

## BufferInscriber

Provides a manual, low-level interface to directly interact with buffers.

### Methods

* `BufferInscriber.new(instance: buffer?): BufferInscriber`
* `BufferInscriber:IncrementPointer(amount: number): BufferInscriber`
* `BufferInscriber:SetPointer(amount: number): BufferInscriber`
* `BufferInscriber:Write(memorySize: number, dataType: string, value: number): BufferInscriber`
* `BufferInscriber:Read(memorySize: number, dataType: string): any`
* `BufferInscriber:WriteType(dataType: string, data: any): BufferInscriber`
* `BufferInscriber:ReadType(dataType: string, offset: number?): (any, number)`
* `BufferInscriber:SetBuffer(buffer: buffer?): BufferInscriber`
* `BufferInscriber:Destroy(): nil`

### Fields

* `pointer: number`
* `buffer: buffer`

---

# Utilities

## SizeOf

```lua
function SizeOf(typeMarker: string): number
```

Returns the byte size of a particular data type.

## TypeToId

```lua
function TypeToId(dataType: string): number
```

Converts a type name to its corresponding internal ID.

## IdToType

```lua
function IdToType(dataId: number): string?
```

Converts an internal ID back to a type name.

## ResolveType

```lua
function ResolveType(data: any): string?
```

Attempts to infer the buffer type from an input value.

---

# Buffer Types

Mapping of types to buffer settings:

| Type         | Size (bytes) | Module Required  |
| ------------ | ------------ | ---------------- |
| BrickColor   | 24           | BrickColor.lua   |
| CFrame       | 96           | CFrame.lua       |
| Color3       | 24           | Color3.lua       |
| DateTime     | 8            | DateTime.lua     |
| TweenInfo    | 19           | TweenInfo.lua    |
| UDim         | 16           | UDim.lua         |
| UDim2        | 32           | UDim2.lua        |
| Vector2      | 16           | Vector2.lua      |
| Vector2int16 | 4            | Vector2int16.lua |
| Vector3      | 24           | Vector3.lua      |
| Vector3int16 | 6            | Vector3int16.lua |
| unsigned8    | 1            | Unsigned8.lua    |
| unsigned16   | 2            | Unsigned16.lua   |
| unsigned32   | 4            | Unsigned32.lua   |
| int8         | 1            | Integer8.lua     |
| int16        | 2            | Integer16.lua    |
| int32        | 4            | Integer32.lua    |
| float32      | 4            | Float32.lua      |
| float64      | 8            | Float64.lua      |

# Special Constants

* **F32\_NUMERICAL\_MIN** = `-3.4e+38`
* **F32\_NUMERICAL\_MAX** = `3.4e+38`
* **I8\_NUMERICAL\_MIN** = `-128`
* **I8\_NUMERICAL\_MAX** = `127`
* **I16\_NUMERICAL\_MIN** = `-32,768`
* **I16\_NUMERICAL\_MAX** = `32,767`
* **U8\_NUMERICAL\_MIN** = `0`
* **U8\_NUMERICAL\_MAX** = `255`
* **U16\_NUMERICAL\_MIN** = `0`
* **U16\_NUMERICAL\_MAX** = `65535`
* **U32\_NUMERICAL\_MIN** = `0`
* **U32\_NUMERICAL\_MAX** = `4294967295`

# Internal Helper Functions

* **\_resolveNumberType(number)**: Resolves what number type to serialize based on the input number.

# Example Usage

```lua
local BufferLibrary = require(path_to_BufferLibrary)

local buffer = BufferLibrary.Buffer.new({
    MakeBuffer = true,
    Template = {"unsigned8", "unsigned16"}
})

local serialized = buffer:Serialize({100, 5000})
local deserialized = buffer:ImportBuffer(serialized):Deserialize()

print(deserialized[1], deserialized[2]) -- Output: 100, 5000
```

---

# Notes

* **Buffer** is higher-level, easier, and automated.
* **BufferInscriber** is lower-level, requires more manual control.
* Manual buffer handling gives flexibility but needs correct pointer math.
* All types must have corresponding modules in the `Types` folder.

---

# License

**Copyright (c) 2024 Madonox**

**All rights reserved.**
