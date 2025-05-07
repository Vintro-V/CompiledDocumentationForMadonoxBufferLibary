# Buffer Library Documentation

## Overview

The Buffer Library, created by Madonox in 2024, is a serialization and deserialization system designed for Lua (specifically Roblox Lua). It provides two main classes:

* **Buffer**: For structured serialization/deserialization based on a template.
* **BufferInscriber**: For manual, lower-level control over buffer management.

It also provides utility functions to map data types to internal buffer representations.

---

## Types

### BufferOptions

```lua
-- Defines configuration options for Buffer creation
BufferOptions = {
    MakeBuffer: boolean,  -- Whether to create a buffer immediately.
    Template: {string}    -- A list of type markers for serialization.
}
```

### BufferModule

```lua
BufferModule = {
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
    template: {[any]: any},
    __index: BufferModule,
    Types: {string: any}
}
```

### BufferInscriber

```lua
BufferInscriber = {
    new: (buffer?) -> BufferInscriber,
    IncrementPointer: (BufferInscriber, number) -> BufferInscriber,
    SetPointer: (BufferInscriber, number) -> BufferInscriber,
    Write: (BufferInscriber, number, string, number) -> BufferInscriber,
    Read: (BufferInscriber, number, string) -> number,
    WriteType: (BufferInscriber, string, any) -> BufferInscriber,
    ReadType: (BufferInscriber, string, number?) -> (any, number),
    SetBuffer: (BufferInscriber, buffer?) -> BufferInscriber,
    Destroy: (BufferInscriber) -> nil,
    pointer: number,
    buffer: buffer,
    __index: BufferInscriber
}
```

### BufferLibrary

```lua
BufferLibrary = {
    Buffer: BufferModule,
    Inscriber: BufferInscriber,
    SizeOf: (string) -> number,
    TypeToId: (string) -> number,
    IdToType: (number) -> string?,
    ResolveType: (any) -> string?
}
```

---

## Classes and Functions

### Buffer

* `Buffer.new(options: BufferOptions) -> BufferModule`
* `Buffer:Serialize(data: {any}) -> buffer`
* `Buffer:Deserialize() -> {any}`
* `Buffer:Write(memorySize: number, dataType: string, value: number) -> BufferModule`
* `Buffer:Read(memorySize: number, dataType: string) -> any`
* `Buffer:IncrementPointer(amount: number) -> BufferModule`
* `Buffer:SetTemplate(template: {string}) -> BufferModule`
* `Buffer:MakeNewBuffer() -> BufferModule`
* `Buffer:ImportBuffer(buffer: buffer) -> BufferModule`
* `Buffer:Destroy() -> nil`

### BufferInscriber

* `BufferInscriber.new(buffer?: buffer) -> BufferInscriber`
* `BufferInscriber:IncrementPointer(amount: number) -> BufferInscriber`
* `BufferInscriber:SetPointer(amount: number) -> BufferInscriber`
* `BufferInscriber:Write(memorySize: number, dataType: string, value: number) -> BufferInscriber`
* `BufferInscriber:Read(memorySize: number, dataType: string) -> number`
* `BufferInscriber:WriteType(dataType: string, data: any) -> BufferInscriber`
* `BufferInscriber:ReadType(dataType: string, offset?: number) -> (any, number)`
* `BufferInscriber:SetBuffer(buffer?: buffer) -> BufferInscriber`
* `BufferInscriber:Destroy() -> nil`

### Utility Functions

* `SizeOf(typeMarker: string) -> number`
* `TypeToId(dataType: string) -> number`
* `IdToType(dataId: number) -> string?`
* `ResolveType(data: any) -> string?`

---

## BUFFER\_TYPES

A dictionary mapping type names to tuples:

```lua
["TypeName"] = {TypeId: number, SizeInBytes: number, HandlerModule: ModuleScript}
```

Supported types include:

* BrickColor
* CFrame
* Color3
* DateTime
* TweenInfo
* UDim
* UDim2
* Vector2
* Vector2int16
* Vector3
* Vector3int16
* unsigned8
* unsigned16
* unsigned32
* int8
* int16
* int32
* float32
* float64

---

## Internal Helpers

### SizeOfTemplate

```lua
SizeOfTemplate(template: {string}) -> number
-- Calculates total byte size of a template.
```

### \_resolveNumberType

Figures out the most efficient type for a given number:

* Chooses int8, int16, int32, float32, or float64.

### \_Numerical Ranges

* float32: ±3.4e38
* int8: ±128
* int16: ±32,768
* unsigned8: 0-255
* unsigned16: 0-65535
* unsigned32: 0-4,294,967,295

---

## Notes

* The library relies on a separate `buffer` module and type handler modules inside a "Types" folder.
* `Buffer` is higher level and safer.
* `BufferInscriber` is lower-level and allows full manual control (more error-prone).
* Use `ResolveType` to auto-detect the correct type for number serialization.

## Example Usage

```lua
local BufferLibrary = require(game.ReplicatedStorage.BufferLibrary)
local Buffer = BufferLibrary.Buffer

local myBuffer = Buffer.new({
    MakeBuffer = true,
    Template = {"int32", "float32", "Color3"}
})

myBuffer:Serialize({123, 456.789, Color3.new(1, 0, 0)})
local data = myBuffer:Deserialize()
print(data)
```

---

*End of Documentation*
