# pdm-make
tool.pdm.scripts for building à la [make](https://en.wikipedia.org/wiki/Make_(software))

In `pyproject.toml`:

```toml
[tool.pdm.scripts]
# https://pdm.fming.dev/usage/scripts/
_.env = {SRCDIRS = "netflix tests"}
#
make.shell = """pdm run tags &&
            pdm run lint &&
            pdm run test &&
            pdm build
"""
rebuild.shell = """pdm run clean-all &&
            pdm install &&
            pdm run make
"""
#
lint.shell = """pdm run black &&
            pdm run flake8 &&
            pdm run isort &&
            pdm run pylint
"""
black.shell = "pdm run python -m black -q $SRCDIRS"
flake8.shell = "pdm run python -m flake8 $SRCDIRS"
isort.shell = "pdm run python -m isort $SRCDIRS"
pylint.shell = "pdm run python -m pylint $SRCDIRS"
#
test.shell = "[ -d tests ] && pdm run pytest || true"
pytest.shell = """pdm run python -m pytest \
            --exitfirst --showlocals --verbose tests
"""
#
publish.shell = """cd dist; echo *.whl |
            cpio -pdmuv `pip config get global.find-links`
"""
#
tags.shell = "ctags -R $SRCDIRS __pypackages__"
clean-all.shell = "pdm run clean; rm -rf tags __pypackages__ dist"
clean.shell = """find . -type f -name '*.py[co]' -delete &&
            find . -type d -name __pycache__ -delete
"""
doc.shell = """export COLUMNS=97;
            pdm run netflix --help |
            pdm run mandown --width 89 --use-config >README.md
"""
#xinstall.shell = "pip install --user dist/*.whl"
#uninstall.shell = "pip uninstall $PACKAGE"
#reinstall.shell = "pdm run uninstall; pdm run install"
```

In `~/.vimrc`:

```vim
if filereadable('pyproject.toml')
    set makeprg=pdm\ run\ make
endif
```

In `vim`, run `:make`, then use `:cnext` and `:cprevious` to move through errors.
