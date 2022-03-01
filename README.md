# pdm-make
tool.pdm.scripts for building à la [make](https://en.wikipedia.org/wiki/Make_(software))

In `pyproject.toml`:
```
[tool.pdm.scripts]
make.shell = "pdm run tags && pdm run lint && pdm run test && pdm build"
rebuild.shell = "pdm run clean-all && pdm install && pdm run make"
tags.shell = "ctags -R ."
lint.shell = "pdm run black && pdm run flake8 && pdm run isort && pdm run pylint"
black.shell = "pdm run python -m black -q src tests"
flake8.shell = "pdm run python -m flake8 src tests"
isort.shell = "pdm run python -m isort src tests"
pylint.shell = "pdm run python -m pylint src tests"
test.shell = "[ -d tests ] && pdm run pytest || true"
pytest.shell = "pdm run python -m pytest --exitfirst --showlocals --verbose tests"
publish.shell = "cd dist; echo *.whl | cpio -pdmuv `pip config get global.find-links`"
clean-all.shell = "pdm run clean; rm -rf __pypackages__ dist"
clean.shell = """find . -type f -name '*.py[co]' -delete &&
            find . -type d -name __pycache__ -delete
"""
```

In `~/.vimrc`:
```
if filereadable('pyproject.toml')
    set makeprg=pdm\ run\ make
endif
```

In `vim`, run `:make`, then use `:cnext` and `:cprevious` to move through errors.
