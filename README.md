# pdm-make
tool.pdm.scripts for building à la `make`

```
[tool.pdm.scripts]
make.shell = "pdm run tags && pdm run lint && pdm run test && pdm build"
rebuild.shell = "pdm run clean-all && pdm install && pdm run make"
clean-all.shell = "pdm run clean; rm -rf __pypackages__ dist"
clean.shell = "find . -type f -name '*.py[co]' -delete && find . -type d -name __pycache__ -delete"
tags.shell = "ctags -R ."
lint.shell = "pdm run black && pdm run flake8 && pdm run isort && pdm run pylint"
black.shell = "pdm run python -m black -q tivo tests"
flake8.shell = "pdm run python -m flake8 tivo tests"
isort.shell = "pdm run python -m isort tivo tests"
pylint.shell = "pdm run python -m pylint tivo tests"
test.shell = "[ -d tests ] && pdm run pytest || true"
pytest.shell = "pdm run python -m pytest --exitfirst --showlocals --verbose tests"
publish.shell = "cp -pv dist/*.whl `pip config get global.find-links`"
xinstall.shell = "pip install --user dist/*.whl"
uninstall.shell = "pip uninstall tivo"
reinstall.shell = "pdm run uninstall; pdm run install"
list.shell = "pdm plugin list; pdm run --list"
```
