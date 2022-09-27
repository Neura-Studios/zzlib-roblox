# zzlib-roblox

A refactor of [zerkman/zzlib](https://github.com/zerkman/zzlib) for use in the Roblox engine and through [Wally](https://wally.run/). The wally package can be found at `neura-studios/zzlib`.

This is a pure Lua implementation of a depacker for the zlib DEFLATE(RFC1951)/GZIP(RFC1952) file format.
zzlib also allows the decoding of zlib-compressed data (RFC1950).
Also featured is basic support for extracting files from DEFLATE-compressed ZIP archives
(no support for encryption).

The implementation is pretty fast. It makes use of the built-in bit32 (PUC-Rio
Lua) or bit (LuaJIT) libraries for bitwise operations. Typical run times to
depack lua-5.3.3.tar.gz on a single Core i7-6600U are 0.87s with Lua ≤ 5.2,
0.50s with Lua 5.3, and 0.17s with LuaJIT 2.1.0.

zzlib is distributed under the WTFPL licence. See the COPYING file
or http://www.wtfpl.net/ for more details.

## Usage

There are various ways of using the library.

### Read GZIP/zlib data from a string

Read a file into a string, call the depacker, and get a string with the unpacked file contents, as follows:

```lua
-- import the zzlib library
local zzlib = require(...Packages.zzlib)
...

if use_gzip then
  -- unpack the gzip input data to the 'output' string
  output = zzlib.gunzip(input)
else
  -- unpack the zlib input data to the 'output' string
  output = zzlib.inflate(input)
end
```

### Extract a file from a ZIP archive stored in a string

Read a file into a string, call the depacker, and get a string with the unpacked contents of the chosen file, as follows:

```lua
-- import the zzlib library
local zzlib = require(...Packages.zzlib)
...

-- extract a specific file from the input zip file
output = zzlib.unzip(input,"lua-5.3.4/README")
```

### Process the list of files from a ZIP archive stored in a string

The `zzlib.files()` iterator function allows you to span the whole list of files in a ZIP archive, as follows:

```lua
for _,name,offset,size,packed,crc in zzlib.files(input) do
  print(string.format("%10d",size),name)
end
```

During such a loop, the `packed` boolean variable is set to `true` if the current file is packed. You may then decide to unpack it using this function call:

```lua
output = zzlib.unzip(input,offset,crc)
```

If the file is not packed, then you can directly extract its contents using `string.sub`:

```lua
output = input:sub(offset,offset+size-1)
```
