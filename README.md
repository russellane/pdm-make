# pdm-make
[tool.pdm.scripts](https://pdm.fming.dev/usage/scripts/) for building à la [make](https://en.wikipedia.org/wiki/Make_(software))

## pyproject.toml

```toml
[tool.pdm.scripts]
_.env = {PKGNAME="wumpus", PKGDIRS = "wumpus tests", VERBOSE=""}

rebuild.shell = "pdm run clean && pdm install && pdm build"
# depends on: `pip install --user pdm-bump`
bump-micro.shell = "pdm bump micro && pdm run rebuild"

# .vimrc: set makeprg=pdm\ build
pre_build.shell = "pdm run tags && pdm run lint && pdm run test && pdm run doc"
post_build.shell = "pdm run version"

help.shell = "pdm run python -m $PKGNAME --help"
version.shell = "pdm run python -m $PKGNAME --version"
tags.shell = "ctags -R --languages=python $PKGDIRS __pypackages__"

lint.shell = "pdm run black && pdm run flake8 && pdm run isort && pdm run pylint"
black.shell = "pdm run python -m black -q $VERBOSE $PKGDIRS"
flake8.shell = "pdm run python -m flake8 $VERBOSE $PKGDIRS"
isort.shell = "pdm run python -m isort $VERBOSE $PKGDIRS"
pylint.shell = "pdm run python -m pylint $VERBOSE $PKGDIRS"

test.shell = "pdm run pytest"
pytest.shell = "pdm run python -m pytest --exitfirst --showlocals --verbose tests"

publish.shell = """
cd dist; echo *.whl |
cpio -pdmuv `pip config get global.find-links`
"""

clean.shell = """
rm -rf ./__pypackages__ ./.pytest_cache ./dist ./tags;
find . -type f -name '*.py[co]' -delete &&
find . -type d -name __pycache__ -delete
"""

doc.shell = """
export COLUMNS=97;
pdm run python -m $PKGNAME --help |
pdm run python -m mandown --width 89 --use-config >README.md
"""

#xxinstall.shell = "pip install --user dist/*.whl"
#uninstall.shell = "pip uninstall $PACKAGE"
#reinstall.shell = "pdm run uninstall; pdm run xxinstall"
```

## ~/.vimrc

```vim
if filereadable('pyproject.toml')
    set makeprg=pdm\ build
endif
```

## vim
Run `:make`, then use `:cnext` and `:cprevious` to move through errors.
