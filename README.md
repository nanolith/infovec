infovec
=======

Infovec is a simple daemon and API that allows for the storage and retrieval of
information based on a vector search.  This project is inspired by the [MIT
Remembrance Agent][remem-agent] originally designed and written by Bradley J.
Rhodes.  This project does not share any source code with the Remembrance Agent;
it was built from the ground up to solve a similar problem and to provide a
simple client library to integrate with IDEs and other text editors.

[remem-agent]: https://alumni.media.mit.edu/~rhodes/Papers/remembrance.html

Vector Search Daemon
--------------------

At the core of infovec is `vectord`. This is a daemon that maintains an index of
all relevant documents. A client can request that files be indexed by a
`vectord` instance and update this indexing when the files change.  This client
can also set up a custom profile for any files indexed to customize stop words
or even to provide a custom search index. Custom search indexes can be used to
filter metadata, such as class names, function names, variable names, etc.
This metadata can be used, for instance, to search for function declarations,
function definitions, or function use.  Since it would be impossible to support
every programming language, `vectord` and the client API leaves this level of
indexing up to the client.  Users can write plug-ins to support Java, C#,
Python, Haskell, Lisp, C/C++, etc.  There are plenty of third party tools and
libraries that can reduce source code into an AST where such metadata can be
collected.

Instead of maintaining a separate index file that must be updated, `vectord`
keeps its indexes in RAM.  Most modern systems have plenty of RAM to spare for
this purpose.  This also simplifies start-up at the cost of boot time.  When
`vectord` is started, client code and scripts need to be run to index files. In
practice, this doesn't take long for smaller knowledge bases. For larger
knowledge bases, the client API provides a mechanism for retrieving the index
data, which can be stored in cache files and loaded directly on startup.

Built-in Tools
--------------

infovec has a few built-in tools that use the client API to index text files,
Markdown files, PDF and PS files, and mail files.

Model Checking
--------------

All of `vectord` and the client API is fully model checked using [CBMC][cbmc]. A
tutorial for how to use CBMC based on its use in infovec will be published soon.

[cbmc]: https://www.cprover.org/cbmc/

All libraries and APIs that `vectord` uses are mocked in the model checker using
shadow libraries.  These shadow libraries simulate the contractual behavior of
the original libraries while simplifying the simulation overhead of the model
checker. For instance, it is not necessary to fully simulate file I/O, and
instead, the shadow methods for I/O simulate resource constraints and each of
the possible return codes or error states of the I/O library.  Likewise, file
descriptors are simulated using allocated resources so that any behavior that
fails to properly close opened files or to use file descriptors after they have
been closed will cause error states in the model checker.
