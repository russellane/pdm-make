# pdm-make
[tool.pdm.scripts](https://pdm.fming.dev/usage/scripts/) for building à la [make](https://en.wikipedia.org/wiki/Make_(software))

## pyproject.toml

```toml
[tool.pdm.scripts]
# .vimrc: set makeprg=pdm\ build
pre_build.shell = """set -x;
ctags -R --languages=python __pypackages__ wumpus tests;
python -m black -q wumpus tests &&
python -m isort wumpus tests &&
python -m flake8 wumpus tests &&
python -m pytest --exitfirst --showlocals --verbose tests &&
{ export COLUMNS=97; python -m wumpus --help |
python -m mandown --width 89 --use-config >README.md; };
"""
rebuild.shell = "set -x; rm -f pdm.lock; pdm run clean && pdm install && pdm build"
clean.shell = """set -x;
rm -rf __pypackages__ .pytest_cache dist tags;
find . -type f -name '*.py[co]' -delete &&
find . -type d -name __pycache__ -delete
"""
# bump depends on: `pip install --user pdm-bump`
bump-micro.shell = "set -x; pdm bump micro && pdm run rebuild"
publish.shell = """set -x; cd dist; echo *.whl |
cpio -pdmuv `pip config get global.find-links`
"""

# Don't name `install` else `pre_install` and/or `post_install` will also run.
#xxinstall.shell = "set -x; unset PYTHONPATH; pip install --user wumpus"
#uninstall.shell = "set -x; unset PYTHONPATH; pip uninstall wumpus"
xxinstall.shell = "set -x; pipx install wumpus"
uninstall.shell = "set -x; pipx uninstall wumpus"
reinstall.shell = "set -x; pdm run uninstall; pdm run xxinstall"
```

## ~/.vimrc

```vim
if filereadable('pyproject.toml')
    set makeprg=pdm\ build
endif
```

## vim
Run `:make`, then use `:cnext` and `:cprevious` to move through errors.
