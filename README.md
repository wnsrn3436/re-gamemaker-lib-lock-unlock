# GameMaker LIB Lock/Unlock Tool

[한국어](README.ko.md)

A tool that locks a GameMaker action library file (`.lib`) so that it cannot be edited even when opened in the Library Maker, or removes an existing lock. It was made by reverse engineering GameMaker's lib file format.

<p>
  <img src="docs/screenshots/screenshot-1.png" width="480" alt="Lock/unlock selection screen">
</p>


## How to use

Download it from Releases and run it, pick lock or unlock, then choose a `.lib` file. A completion message appears, and the tool closes when you click OK. It edits the file in place, so it is a good idea to back it up first.


## How it works

The library ID written inside a `.lib` decides whether it is locked. The file starts with a 4-byte version and the name length, and the ID follows right after the name, so the tool reads the name length from the byte at offset 4 and changes the two bytes at offset `8 + name length`. GameMaker treats a library with a small ID as a built-in library and blocks editing it. Locking therefore overwrites that spot with `01 00` to make the ID 1, and unlocking writes random values to turn it back into a large ID.

```gml
open = file_bin_open(file_open, 2)
file_bin_seek(open, 4)
seek = file_bin_read_byte(open)        // name length
file_bin_seek(open, 8+seek)            // library ID position

// lock: set the ID to 1
file_bin_write_byte(open, 1)
file_bin_write_byte(open, 0)

// unlock: random large ID
file_bin_write_byte(open, round(random(230))+10)
file_bin_write_byte(open, round(random(230))+10)
```

Comparing a locked `.lib` and an unlocked `.lib` in a hex editor showed that these two bytes decide the lock.


## Files

| Path | Contents |
|---|---|
| `source/gamemaker-lib-lock-unlock.gmk` | Original project file |
| `source/split/` | Text tree produced by GmkSplitter |
| `docs/screenshots/` | Screenshots |
| Releases | Executable and usage notes |


## License

zlib. See [LICENSE](LICENSE).
