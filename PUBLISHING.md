# Publishing FSSG to PyPI

Follow the steps below to build and publish the `fssg` Python package. The process uses [PyPI API tokens](https://pypi.org/help/#apitokens), which are preferred over uploading with your account password.

---

## 1. Prerequisites
- Python 3.9+
- A [PyPI account](https://pypi.org/)
- An API token with publish rights (create one in **PyPI → Account settings → API tokens**)
- Optional but recommended: upload to [TestPyPI](https://test.pypi.org/) first to verify the package

Install the build tooling in your virtualenv:

```bash
pip install --upgrade build twine
```

---

## 2. Clean working tree
Make sure `git status` reports no uncommitted changes (apart from files you intend to publish). The package metadata lives under `fssg/pyproject.toml`; confirm the version is correct and that `LICENSE`, `README.md`, and `CONTRIBUTING.md` exist at the repository root.

---

## 3. Build source and wheel distributions
From the repository root:

```bash
python -m build
```

This creates `dist/fssg-<version>.tar.gz` and `dist/fssg-<version>-py3-none-any.whl`.

---

## 4. Upload to TestPyPI (optional dry run)

```bash
twine upload --repository testpypi dist/*
```

When prompted for credentials:
- **Username:** `__token__`
- **Password:** paste your TestPyPI API token

Install from TestPyPI to verify everything works:

```bash
python -m pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple fssg
```

---

## 5. Upload to PyPI
Once satisfied with the TestPyPI run:

```bash
twine upload dist/*
```

Again use:
- **Username:** `__token__`
- **Password:** paste your PyPI API token

Twine will report the upload status. If you get validation errors, fix them, rebuild, and rerun the upload.

---

## 6. Post-publish checklist
- Confirm the release appears at <https://pypi.org/project/fssg/>
- Tag the release in git: `git tag vX.Y.Z && git push --tags`
- Update release notes / changelog if applicable
- Optionally, draft a GitHub release referencing the new PyPI version

---

## 7. Common issues
- **`HTTPError: 400 Bad Request`** – usually indicates version reuse. Bump the version in `pyproject.toml` and `fssg/__init__.py`.
- **Missing files** – ensure `README.md` and `LICENSE` are referenced correctly (`project.readme`, `project.license`).
- **Wheel missing package data** – confirm `tool.setuptools.packages.find.include` captures all modules; add explicit `package_data` if necessary.

---

Maintainer: **Emil Rokossovskiy** (<ecsiar@gmail.com>)
