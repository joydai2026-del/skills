---

name: notion-cli
description: >-
  Use the Notion CLI (`ntn`) to interact with the Notion API, manage workers,
  and upload files. Use when the user asks to "call the Notion API", "deploy a
  worker", "upload a file to Notion", "create a page", "query a database", or
  any task involving the `ntn` command.
---

# Notion CLI

## Look things up before answering

The CLI is self-documenting. Always prefer running these commands over guessing
syntax or relying on memorized knowledge:

- `ntn api ls`, list every public API endpoint.
- `ntn api <path> --help`, show methods, doc links, and usage for an endpoint.
- `ntn api <path> --docs`, print the full official docs for an endpoint.
- `ntn api <path> --spec`, print a reduced OpenAPI fragment (useful for
  understanding request/response schemas).
- `ntn pages get <page-id>`, retrieve a page as Markdown. Use this to read page
  content.
- `ntn <command> --help`, help for any command or subcommand.

## Install

```bash
curl -fsSL https://ntn.dev | bash
```

## Authentication

- The CLI automatically uses `NOTION_API_TOKEN` when it is set.
- Check `NOTION_API_TOKEN` first. If it is already set, prefer using it instead
  of telling the user to run `ntn login`.
- `ntn login` / `ntn logout`, log the CLI in or out (only use if not using
  `NOTION_API_TOKEN`). `ntn login` requires the user to visit a URL in a web
  browser.

## `ntn api`

Run `ntn api --help` for full syntax. Quick summary:

```bash
# GET with query param
ntn api v1/users page_size==100

# POST with inline body fields
ntn api v1/pages parent[page_id]=abc123

# POST with JSON body
ntn api v1/pages -d '{"parent":{"page_id":"abc123"}}'
```

The method is inferred (GET by default, POST when a body is present). Override
with `-X METHOD`.

### Markdown for pages and comments

Prefer `ntn pages create` / `ntn pages update` for Markdown page content. Use
the `markdown` field when creating or updating comments via `ntn api`.

```bash
# Comment with markdown
ntn api v1/comments -d '{"parent":{"page_id":"abc123"},"markdown":"Here is a [link](https://example.com) and **bold text**."}'

# Page with markdown body
ntn pages create --parent page:abc123 --content '## Heading\n\nSome *formatted* content.'
```

The `markdown` field supports inline formatting (bold, italic, code, links, etc.).
Only fall back to `rich_text` if you need features that Markdown cannot express (e.g. mentions, custom emoji, or colors).

## `ntn files`

Convenience wrapper around the File Uploads API.

```bash
ntn files create < image.png
ntn files create --external-url https://example.com/photo.png
ntn files list
ntn files get <upload-id>
```

## `ntn workers`

Manage Notion workers (deploy, list, execute, etc.). Run `ntn workers --help`
for subcommands.

```bash
ntn workers new my-worker        # scaffold a new project
ntn workers deploy               # deploy from current directory
ntn workers ls                   # list workers
ntn workers exec <capability>    # execute a capability
```

---

## Traps worth knowing before you touch a page that has children

Traps that each cost real time. Read before touching a page that has children.

**1. `pages update` is a FULL REPLACE, and it REFUSES when the page has child pages or databases.**
The error reads `This operation would delete N child page(s) or database(s)`. That is the tool protecting you, not a bug. **Do NOT reach for `--allow-deleting-content` to get past it**, that really does delete the children, including a database and all its rows.
The correct order when you need to rewrite a parent page that has children:
```
1. save the children's content to local files first
2. trash the child pages (`ntn pages trash <id> --yes`) and/or the database
   (`ntn api /v1/databases/<id> -X PATCH -d '{"in_trash":true}'`)
3. ntn pages update <parent> --content "$(cat new.md)"
4. recreate the database and its rows
```
Building children first and replacing the parent afterwards will always fail.

**2. `pages trash` needs `--yes` in a non-interactive shell.** Without it: `Cannot confirm in a non-interactive environment.` In a script the `&&` chain then silently skips the rest, so the step looks like it ran.

**3. `ntn api` argument order is `ntn api <PATH> -X <METHOD> -d '<json>'`.**
Putting the method first (`ntn api POST /v1/databases ...`) makes the CLI parse the path as an INPUT and fail with `unexpected input: "/v1/databases"`. Also, `-d @file` is NOT supported, pass the JSON inline with `-d "$(cat file.json)"`.

**4. A large page makes `pages update` slow enough to look hung** (2+ minutes, sometimes far more), while a small page returns in ~2 seconds. Run big updates with `run_in_background: true` rather than raising the timeout and blocking.

**5. Creating a database**: `POST /v1/databases` with `parent.page_id` plus `initial_data_source.properties`. The response's `id` is the DATABASE id, but rows are created against the **data source id** (`data_sources[0].id`) with `parent: {type: "data_source_id", data_source_id: "..."}`. Using the database id for rows fails.

**6. To give a database row a body**, create the row first (`POST /v1/pages`), then `ntn pages update <row_id> --content "$(cat body.md)"`. The row id comes back as the plain `id` field.

### Structural advice
When a Notion page is the user's single hub, prefer **one parent page plus a database of child pages** over one giant page. Appending is not supported by `ntn`, so a growing single page means an ever-slower full replace each time, and it collides with the child-page rule above.

**7. Typed values in an `ntn api` body use `:=`, and `--json` does not exist.**
`ntn api /v1/search 'query=example' 'page_size=10'` fails with `body.page_size should be a number`
because `param=value` always sends a STRING. Use `param:=value` for numbers/booleans:
`ntn api /v1/search 'query=example' 'page_size:=10'`. (`==` is for query-string params on GET,
`:=` for typed body fields on POST.) There is no `--json` flag; passing one errors.
