Python Wisdom
=============

This file contains lessons in Python that I felt needed to be documented because I see them, and I consistently
comment on them during code reviews.

Testing
-------

Use pytest & native Python `assert` statements.  It makes debugging test failures so much eaiser.

See: https://gist.github.com/borgstrom/bbe8237c2f821f8e63a40ad151ebc66d


Logging
-------

### String Interpolation

Using `"hello {0}".format(thing)` is almost always the correct choice for string interpolation.  In the logging package,
however, it is preferrable to use it's built-in interpolation to avoid the cost of formatting the string if it's not
needed.

Consider the following:

    log.info("About to make 10,000 requests")
    for x in xrange(10000):
        response = make_request()
        log.debug("Response: {0}".format(response))

        # code continues...

Even if we're only at the `INFO` level we still pay to format the whole response into a string on every iteration just
to have it thrown away by the logger.

Instead, use the built-in interpolation:

    log.info("About to make 10,000 requests")
    for x in xrange(10000):
        response = make_request()
        log.debug("Response: %s", response)

        # code continues...

Since the arguments are passed as references in Python you only pay for the interpolation if you're at that log level.

### Logging exceptions

When logging in an exception handler the Python logging package provides a straight forward method for logging not only
your human-consumable message but also the complete stack trace for the current exception.

This is a common anti-pattern:

    try:
        raise_an_exception()
    except Exception as exc:
        log.error("Something bad happened: %s", str(exc))

Instead, use the `.exception` method on the log handler:

    try:
        raise_an_exception()
    except Exception:
        log.exception("Something bad happened")
