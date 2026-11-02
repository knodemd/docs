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

Small [example spaces](https://github.com/knodemd/format/tree/main/examples) show the format:
permissions and history, a map with positions and attachments, and a note edited together with
Loro updates. They contain synthetic data only; example embedding vectors are made up and no
model is downloaded by their generator. The editing example shows local merging, not device
sync. Work with a copy: opening an example with a newer Knode may upgrade its database schema.
