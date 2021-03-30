![Tests](https://github.com/MagicStack/httptools/workflows/Tests/badge.svg)

httptools is a Python binding for the nodejs HTTP parser.

The package is available on PyPI: `pip install httptools`.


# APIs

httptools contains two classes `httptools.HttpRequestParser`,
`httptools.HttpResponseParser` (fulfilled through
[llhttp](https://github.com/nodejs/llhttp)) and a function for
parsing URLs `httptools.parse_url` (through
[http-parse](https://github.com/nodejs/http-parser) for now).
See unittests for examples.


```python

class HttpRequestParser:

    def __init__(self, protocol):
        """HttpRequestParser

        protocol -- a Python object with the following methods
        (all optional):

          - on_message_begin()
          - on_url(url: bytes)
          - on_header(name: bytes, value: bytes)
          - on_headers_complete()
          - on_body(body: bytes)
          - on_message_complete()
          - on_chunk_header()
          - on_chunk_complete()
          - on_status(status: bytes)
        """

    def get_http_version(self) -> str:
        """Return an HTTP protocol version."""

    def should_keep_alive(self) -> bool:
        """Return ``True`` if keep-alive mode is preferred."""

    def should_upgrade(self) -> bool:
        """Return ``True`` if the parsed request is a valid Upgrade request.
	The method exposes a flag set just before on_headers_complete.
	Calling this method earlier will only yield `False`.
	"""

    def feed_data(self, data: bytes):
        """Feed data to the parser.

        Will eventually trigger callbacks on the ``protocol``
        object.

        On HTTP upgrade, this method will raise an
        ``HttpParserUpgrade`` exception, with its sole argument
        set to the offset of the non-HTTP data in ``data``.
        """

    def get_method(self) -> bytes:
        """Return HTTP request method (GET, HEAD, etc)"""


class HttpResponseParser:

    """Has all methods except ``get_method()`` that
    HttpRequestParser has."""

    def get_status_code(self) -> int:
        """Return the status code of the HTTP response"""


def parse_url(url: bytes):
    """Parse URL strings into a structured Python object.

    Returns an instance of ``httptools.URL`` class with the
    following attributes:

      - schema: bytes
      - host: bytes
      - port: int
      - path: bytes
      - query: bytes
      - fragment: bytes
      - userinfo: bytes
    """
```


# Development

1. Clone this repository with
   `git clone --recursive git@github.com:MagicStack/httptools.git`

2. Create a virtual environment with Python 3.5:
   `python3.5 -m venv envname`

3. Activate the environment with `source envname/bin/activate`

4. Install Cython with `pip install cython`

5. Run `make` and `make test`.


# License

MIT.
