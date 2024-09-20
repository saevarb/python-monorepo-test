# Python monorepo test


The goal of this repo is to figure out a way to have a functioning monorepo in python (using either `uv` or `rye`).



# Goal

`services/my_api` should use the package in `packages/my_package` in a way such that any change to the package is available to the api as soon as it is made, i.e. without having to reinstall the package.

One possibly important tidbit is that we do not have or want to have any code in the root project. If you look at the example at https://docs.astral.sh/uv/concepts/workspaces/#workspace-layouts you can see that the albatross project has a `src/albatross` and `main.py` -- this is not what we need or want. 
We are only interested in being able to use `my_package` in `my_api` and have included the root pyproject.toml file because it seems like it is required to set up the workspaces properly. We are not sure if this is the way to go about it and are open to any and all input -- it is entirely possible I am drawing
too heavily on my experiences using nodejs monorepos and assuming that things should work the same way.


# Current situation / issues

With the current setup, we have achieved mostly what we want with one (possibly unimportant) issue:


1. Starting from a clean slate (no `.venv`), run `uv sync` in the root
2. Observe that it says "resolved 12 packages" but no dependencies appear to have been installed
3. Run `uv sync` inside `services/my_api` and observe that it installs fastapi and some other dependencies, including `my_package`
4. `uv run python hello.py` works as expected inside `my_api`
5. Go back to the root of the project and run `uv sync`
6. Observe that it uninstalls all the dependencies it installed when we ran `uv sync` inside `my_api`
7. The same applies to running `uv sync` inside `my_package` - it uninstalls all the dependencies from `my_api`


This is possibly a non-issue as long as we can properly build/package the application with the workspace dependencies, but it seems counter-intuitive to me that it would uninstall dependencies from sub-packages when run in the root, when it is only using a single venv for the whole project.
