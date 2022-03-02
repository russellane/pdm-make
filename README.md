# pdm-make
tool.pdm.scripts for building à la [make](https://en.wikipedia.org/wiki/Make_(software))

In `pyproject.toml`:

```toml
[tool.pdm.scripts]
# https://pdm.fming.dev/usage/scripts/

_.env = {PKGNAME="netflix", PKGDIRS = "netflix tests", VERSION_FILE = "netflix/__version__.py"}

rebuild.shell = "pdm run clean-all && pdm install && pdm build"
pre_build.shell = "pdm run tags && pdm run lint && pdm run tests && pdm run doc"
post_build.shell = "pdm run version"

help.shell = "pdm run python -m $PKGNAME --help"
version.shell = "pdm run python -m $PKGNAME --version"
tags.shell = "ctags -R --languages=python $PKGDIRS __pypackages__"

lint.shell = "pdm run black && pdm run flake8 && pdm run isort && pdm run pylint"
black.shell = "pdm run python -m black -q $PKGDIRS"
flake8.shell = "pdm run python -m flake8 $PKGDIRS"
isort.shell = "pdm run python -m isort $PKGDIRS"
pylint.shell = "pdm run python -m pylint $PKGDIRS"

tests.shell = "pdm run pytest"
pytest.shell = "pdm run python -m pytest --exitfirst --showlocals --verbose tests"

bump-patch.shell = """
OLD=`sed -n 's/^__version__ = "\\(.*\\)"/\\1/p' <$VERSION_FILE`;
NEW=`pdm run semver -i patch $OLD`;
echo Bumping version patch level: $OLD '->' $NEW;
echo "\\"\\"\\"Version.\\"\\"\\"\\n\\n__version__ = \\"$NEW\\"" >$VERSION_FILE
"""
#bump-minor.shell = """
#OLD=`sed -n 's/^__version__ = "\\(.*\\)"/\\1/p' <$VERSION_FILE`;
#NEW=`pdm run semver -i minor $OLD`;
#echo Bumping version minor level: $OLD '->' $NEW;
#echo "\\"\\"\\"Version.\\"\\"\\"\\n\\n__version__ = \\"$NEW\\"" >$VERSION_FILE
#"""
#bump-major.shell = """
#OLD=`sed -n 's/^__version__ = "\\(.*\\)"/\\1/p' <$VERSION_FILE`;
#NEW=`pdm run semver -i major $OLD`;
#echo Bumping version major level: $OLD '->' $NEW;
#echo "\\"\\"\\"Version.\\"\\"\\"\\n\\n__version__ = \\"$NEW\\"" >$VERSION_FILE
#"""
#bump-micro.shell = """VERSION=`awk '/^__version__/ {
#                split(substr($3, 2), a, ".");
#                print a[1] "." a[2] "." a[3] + 1}' <$VERSION_FILE`;
#            echo Bumping version MICRO level: $VERSION;
#            echo "\\"\\"\\"Version.\\"\\"\\"\\n\\n__version__ = \\"$VERSION\\"" >$VERSION_FILE
#"""
#bump-minor.shell = """VERSION=`awk '/^__version__/ {
#                split(substr($3, 2), a, ".");
#                print a[1] "." a[2] + 1 ".0"}' <$VERSION_FILE`;
#            echo Bumping version MINOR level: $VERSION;
#            echo "\\"\\"\\"Version.\\"\\"\\"\\n\\n__version__ = \\"$VERSION\\"" >$VERSION_FILE
#"""
#bump-major.shell = """VERSION=`awk '/^__version__/ {
#                split(substr($3, 2), a, ".");
#                print a[1] + 1 ".0.0"}' <$VERSION_FILE`;
#            echo Bumping version MAJOR level: $VERSION;
#            echo "\\"\\"\\"Version.\\"\\"\\"\\n\\n__version__ = \\"$VERSION\\"" >$VERSION_FILE
#"""

publish.shell = """
cd dist; echo *.whl |
cpio -pdmuv `pip config get global.find-links`
"""

clean-all.shell = "pdm run clean; rm -rf tags __pypackages__ dist"
clean.shell = """
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

In `~/.vimrc`:

```vim
if filereadable('pyproject.toml')
    set makeprg=pdm\ run\ make
endif
```

In `vim`, run `:make`, then use `:cnext` and `:cprevious` to move through errors.
