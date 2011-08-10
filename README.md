# GameMaker LIB Lock/Unlock Tool

A tool that locks or unlocks a GameMaker action library file (`.lib`) for editing. A locked `.lib` cannot be opened and edited in the Library Maker. It was made by reverse engineering GameMaker's lib file format.

<p>
  <img src="docs/screenshots/screenshot-1.png" width="480" alt="Lock/unlock selection">
</p>


## How to use

Download it from Releases and run it, pick lock or unlock, then choose a `.lib` file. A completion message appears, and it closes once you dismiss it. It edits the file in place, so back it up first.


## How it works

Whether a `.lib` is locked is decided by the library ID written inside it. The file starts with a 4 byte version and the name length, and the ID follows right after the name, so the byte at offset 4 gives the name length, and the two bytes at `8 + name length` are the ones changed. GameMaker treats a library with a small ID as a built in one and refuses to let it be edited. Lock therefore overwrites that spot with `01 00` to make the ID 1, and unlock writes random values to give it a large ID again.

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

Comparing a locked and an unlocked `.lib` in a hex editor is how these two bytes were found to be what separates the two.


## Files

| Path | Contents |
|---|---|
| `source/gamemaker-lib-lock-unlock.gmk` | Original project file |
| `source/split/` | Text tree produced by GmkSplitter |
| `docs/screenshots/` | Screenshots |
| Releases | Executable and usage notes |


## License

zlib. See [LICENSE](LICENSE).
