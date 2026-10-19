# 5 - The .knode format

A space is a `.knode` file. The Finder shows it as one file; inside it is a folder.

```
work.knode/
├── manifest.json   version, id and name
├── knode.db        SQLite: nodes, links, history, permissions, search
├── blobs/          attachments
└── sync/           changes for syncing
```

- **Every node is Markdown**: CommonMark with GitHub Flavored Markdown and `[[links]]`.
- **Links are kept by id**, so renaming a node never breaks a link.
- **Open:** the format is documented and Apache 2.0, so anyone can read and write it.

The full specification is in [knodemd/format](https://github.com/knodemd/format). Why it is built
this way is in decisions [0005](https://knode.md/handbook/decisions/0005-architecture) and
[0006](https://knode.md/handbook/decisions/0006-markdown).
