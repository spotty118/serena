# Contributing to Serena

Serena is under active development. We are just discovering what it can do and where the limitations lie.

Feel free to share your learnings by opening open new issues, feature requests and extensions.

## Developer Environment Setup

You can have a local setup via `poetry` or a docker interpreter-based setup. 
The repository is also configured to seamlessly work within a GitHub Codespace. See the instructions
for the various setup scenarios below.

Independently of how the setup was done, the virtual environment can be 
created and activated via `poetry` (see below), and the various tasks like formatting, testing, and documentation building
can be executed using `poe`. For example, `poe format` will format the code, including the 
notebooks. Just run `poe` to see the available commands.

### Python (Poetry) setup

You can install a virtual environment with the required dependencies as follows:

1. Install Poetry (if not already installed): [Official Documentation](https://python-poetry.org/docs/#installation)
2. Activate the environment managed by Poetry: (Poetry typically manages this automatically, but you can activate it manually if needed)
    * On Linux/Unix/macOS: `source .venv/bin/activate`
    * On Windows: `.venv\Scripts\activate.bat` (Or use `poetry shell`)
3. Install dependencies (including extras and the project itself in editable mode): `poetry install --all-extras`

### Docker setup

Build the docker image with

```shell
docker build -t serena .
```

and run it with the repository mounted as a volume:

```shell
docker run -it --rm -v "$(pwd)":/workspace serena
```

You can also just run `bash docker_build_and_run.sh`, which will do both things
for you.

Note: For the Windows subsystem for Linux (WSL), you may need to adjust the path for the
volume.

## Adding a New Supported Language

Serena interacts with code through language servers which are included in
the `multilspy` package. It is rather easy to include a new supported language
if an LSP implementation for it exists. You just need to:

1. create a new subclass of `LanguageServer`
2. add a new value to the `Language` enum
3. make a new `elif` case in the `LanguageServer.create` method
4. write minor tests

The subclasses are typically easy to write, have a look at the 
[PyrightLanguageServer](src/multilspy/language_servers/pyright_language_server/pyright_server.py) 
for an example, or at any other implementation to see how non-python
dependencies for language servers are handled there.
There are also some tips from the multilspy admin [here](https://github.com/microsoft/multilspy/issues/5).

⚠️ Important: The LSP allows for lot of optional fields and symbols, so the language servers may differ
in some details, even if they follow the LSP. Therefore you should include some code of the new
language in `test/resources` and add tests for symbolic read operations on that code. Have a look
at `test/multilspy/test_symbol_retrieval.py` for an example of such tests for the python LS.
