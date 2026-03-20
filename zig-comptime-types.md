# Zig comptime: using types as values

In Zig, `type` is a first-class value at compile time. You can pass types to functions, return them, store them in comptime variables.

```zig
fn makeSlice(comptime T: type, allocator: std.mem.Allocator, n: usize) ![]T {
    return allocator.alloc(T, n);
}
```

This enables zero-cost generic containers without runtime dispatch:

```zig
const IntList = std.ArrayList(i32);
const StrList = std.ArrayList([]const u8);
```

`std.ArrayList(T)` is literally a function called at comptime that returns a new struct type specialized for `T`. No vtable, no boxing.

## Inspecting types at comptime

```zig
const info = @typeInfo(T);
switch (info) {
    .Int    => |i| std.debug.print("int: {} bits\n", .{i.bits}),
    .Float  => |f| std.debug.print("float: {} bits\n", .{f.bits}),
    .Struct => |s| std.debug.print("struct with {} fields\n", .{s.fields.len}),
    else    => {},
}
```

`@typeInfo` returns a tagged union — pattern match on it to write generic code that branches on the actual type shape.

## Exhaustive switch on @typeInfo

Zig's switch on a tagged union is exhaustive at compile time. Add  to catch unhandled type classes rather than silently ignoring them.

## Error unions

 is an error union. Use  to propagate or  to handle inline:

Unlike exceptions, error values are just integers — zero overhead.

## Packed structs for bit-level layout

`packed struct` guarantees C-compatible bit layout. Useful for parsing binary protocols without bitshift arithmetic:

