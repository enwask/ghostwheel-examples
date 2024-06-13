# ghostwheel examples

Ghostwheel is an internal inference server for limited use (if you were linked to this repository, it's probably for you—otherwise, it's probably not). This repository's main purpose is to host [a Jupyter notebook with various examples for using ghostwheel](demo.ipynb), which can hopefully serve as a quickstart for your project. Note that you'll need to run the notebook on the internal network (Imperial-WPA) for ghostwheel to be discoverable.

## About ghostwheel

Ghostwheel is implemented as an API layer on top of Ollama, exposing multiple local LLMs running on dedicated GPUs. The ghostwheel API is intended as a replacement for Ollama in projects that already use it as a backend, though some implementations may require modification to include our authorization header in outgoing requests (see [the API docs linked below](#the-ghostwheel-api) for usage of the authentication header).

You can use ghostwheel in a few ways, and examples of some of these methods are demonstrated in the [demo notebook](demo.ipynb):
- Directly as a REST API from your favorite client tech (demonstrated in the notebook with Python's `requests` module)
- With Ollama's Python client, in the form of [the ollama package](https://github.com/ollama/ollama-python) (also demonstrated in the notebook). Ollama provides [an API client for javascript as well](https://github.com/ollama/ollama-js), if you prefer to work with an objectively inferior language
- As a backend for [Langchain with its built-in Ollama support](https://python.langchain.com/v0.2/docs/integrations/providers/ollama/) via the `Ollama`, `ChatOllama` classes etc. (again, in the notebook)
- In a user-friendly web interface via Open WebUI, though this requires some modifications to its Ollama backend: [you can build WebUI from my fork here](https://github.com/enwask/ghostwheel-webui) for a plug-and-play solution
- With any* other project that can use Ollama as a backend, subject to [the caveats described below](#as-an-ollama-replacement)

Note that ghostwheel does *not* work as a drop-in backend host for the Ollama CLI (i.e. with `ollama run` and the `OLLAMA_HOST` environment variable). If you want to use ghostwheel from the command line, you can interact with the API directly via cURL or similar.

## Usage

### The ghostwheel API

Ghostwheel can be accessed from the internal network (Imperial-WPA) at [https://ese-timewarp.ese.ic.ac.uk](https://ese-timewarp.ese.ic.ac.uk). This root path serves the API docs (also hosted at `/docs`) which are available to anyone over the internal network; the endpoints under `/api` require authentication with your API key.

### As an Ollama replacement

For the most part, ghostwheel can work as a replacement for any* Ollama backend. Again, some implementations may require modification to include our authorization header in outgoing requests. Also, any code that expects to administer LLMs on the Ollama server (via `api/pull`, `api/delete` etc.) will likely throw an error when attempting to use these endpoints as they are not exposed through ghostwheel. An example of this is my fork of WebUI linked above; the admin panel displays a 405 response when attempting to delete or modify LLMs on the server.

More speficially, ghostwheel only implements Ollama's primary completion endpoints (`api/generate` and `api/chat`) as well as `api/tags`. This is because the remaining endpoints are only used for administration, management of loaded models, etc., which the end user (that's you!) won't need access to. Additionally, there is a minor difference in the API spec: the `keep_alive` parameter is omitted from both endpoints, for the same reasons listed above. You don't need it.


### Available LLMs

[The ghostwheel API docs](https://ese-timewarp.ese.ic.ac.uk) contain an up-to-date list of valid identifiers for LLMs you can call through ghostwheel. (Again, note this domain is only accessible from the internal network). The docs are regenerated with any change to the backend application, so this list is kept current when any new models are deployed. In addition to the Ollama calls mentioned above, we also provide a `api/list_models` endpoint, should you want to programatically determine which LLMs are available to you.

For more information describing parameters for the completion endpoints and response specifications, [check out the Ollama API docs on GitHub](https://github.com/ollama/ollama/blob/main/docs/api.md).
