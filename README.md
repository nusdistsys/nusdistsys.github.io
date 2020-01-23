# DYOM Site repository

## Installation
If you'd like to run a server to preview your notes, you'll have
to install [Hugo](https://gohugo.io).

Otherwise, just updating the md for your notes and adding the
appropriate Hugo headers should be sufficient.

## Contribute notes!
### With Hugo
1. (If you're making a new page) create a new file for the notes.
   In this project's root folder, run:

```bash
$ hugo new notes/My-Topic.md
```

2. Add your notes in the file
3. Check how it looks by running a dev server

```bash
$ hugo server -D
```

4. Remember to set the `draft` setting in the front matter to false.
5. Open a PR here with your notes as a new file under the `/notes/`
   folder. Remember to request a merge into the `source` branch.

### Without Hugo
1. Add a Markdown file in the `content/notes` directory with your
   notes.
2. Remember to add hugo front matter.
3. Open a PR as above.
