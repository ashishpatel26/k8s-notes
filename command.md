##### Run mkdocs server

```bash
uv run mkdocs serve
```
It will run on 127.0.0.1:8000

##### Deploy on github pages
```bash
uv run mkdocs gh-deploy --clean
```
It will deploy on github pages with current repository

##### Build
```bash
uv run mkdocs build --clean
```
It will build the site and store in site directory