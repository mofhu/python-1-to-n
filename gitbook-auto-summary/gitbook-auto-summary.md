# GitBook-auto-summary

Automatically update Summary.md of a GitBook repo

By [Frank-the-Obscure @ GitHub](https://github.com/Frank-the-Obscure)

Tested in Windows and OS X

# usage

1. `$ python gitbook-auto-summary.py`
  - use argument `-o` to overwrite SUMMARY.md without checking.
2. input directory(it should be the *root* directory of GitBook repo)
3. the auto summary file `SUMMARY.md` will be under the same directory.

# examples

```
folder tree:
.
├── README.md
├── SUMMARY.md
├── md
│   └── SUMMARY.md
├── nomd
└── os-and-os-path.md
```

output SUMMARY.md:

```
# Summary

- [os-and-os-path](./os-and-os-path.md)
- [README](./README.md)
- md
  - [SUMMARY](md/SUMMARY.md)

```
