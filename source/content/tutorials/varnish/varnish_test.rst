.. _varnish_test:

Testing with varnishtest
========================

You may not be aware of this, but `VarnishTest` is an awesome tool.
varnishtest is a sandbox (testing environment) that is completely isolated from
the production environment where one can TEST untested code changes and do
all kinds of out-of-this-world experimentation.

varnishtest protects your "live" servers, reviewed VCL codes and
other collections of data and/or content from severe changes that could be caused
by experimental code. We all know that once in a mission-critical system
phase it is quite difficult to revert back.

Varnish comes with varnishtest pre-installed and runs personalized tests for
whatever case you define. There is also a whole set of pre-defined tests
that you can use to help your understanding and testing.

Here is a list of contexts where you can use `VarnishTest`:

- Testing your Varnish and backend installation
- Configuring your Varnish Cache installation
- When writing complex caching policies in VCL such as:
      - test a cache invalidation method
      - reproduce bugs when filing a report
- When tuning Varnish Cache
- To define and test modules for Varnish Cache extensions (VMOD)
- Writing applications that integrate well with Varnish Cache and utilize all its characteristics.

The Varnish Testcase Language (VTC)
------------------------------------

This is the language that varnishtest understands. The extension used by Varnish
test case files is (.vtc).

Normally, a VTC file describes a scenario with different scripted HTTP-talking
operations and generally one or more Varnish instances (clients) to test.

Naming convention
.................

varnishtest has a whole set of pre-defined tests that you can use if you understand what it is doing. It follows a naming convention, which will help you to understand which set is what type of example.

.. literalinclude:: /content/examples/vtc/vtc_syntax.txt

**Note:** You can use any naming convention to write your test, but a defined
naming convention could help you to avoid rewriting similar tests.

Name the test
.............

varnishtest requires that you name the test.
Here is an example:

.. code-block:: VCL

  varnishtest "This is a varnishtest for testing"


Defining the components
-----------------------

varnishtest requires three components to run the test:

- A server (origin server)
- A Varnish Cache instance
- A client

.. image:: /image/varnishtest_anatomy.svg
  :alt: Sphinx Neo-Hittite
  :align: center
  :scale: 100%

Declaring the server
....................

- A server declaration must start with **s**
- `rxreq` : accepts/receives requests
- `txresp` : transmits/responds to requests
- `- start` : boots server

What follows is an example of declaring a server:

.. literalinclude:: /content/examples/vtc/example.vtc
  :language: VCL
  :lines: 4-7


Declaring the Varnish Cache instance
....................................

- A Varnish Cache instance declaration must start with **v**
- Instance controlled by the manager process
- `- start` : forks a child for this instance from the actual cache process


What follows is an example of declaring a Varnish Cache instance:

.. literalinclude:: /content/examples/vtc/example.vtc
  :language: VCL
  :lines: 9

Declaring a client
..................

- Stimulated client declaration must start with **c**
- `- run` : starts the client

What follows is an example of declaring a Varnish Cache instance:

.. literalinclude:: /content/examples/vtc/example.vtc
  :language: VCL
  :lines: 11-16

In this example, c1 transmits one request and receives one response.

Since Varnish is a proxy instance, the response should be received from the backend
via Varnish Cache (Varnish instance in this case).

- we can see c1 expects Varnish in the via HTTP header field.
- The tilde (~) is used as a match operator of regular expressions
- the exact name of the variable here (e.g. resp.http.via) depends on the version
  of Varnish installed.

Running the test
----------------

- To run the test, issue the command as shown below:

.. code-block:: bash

  varnishtest example.vtc

**Note:** If you are writing your own test, make sure that you point to the
directory where the test is stored.

This is the positive output to expect:

.. code-block:: VCL

  #     top  TEST example.vtc passed (1.709)

If you feel the need to inspect/understand the test better, you can always try the
verbose mode with a `-v` as shown below:

.. code-block:: VCL

  varnishtest -v example.vtc

This is what a verbose output looks like for the ``example.vtc``:

.. literalinclude:: /content/examples/vtc/example_voutput.vtc
  :language: bash

Taking your varnishtest to the next level
------------------------------------------

Connecting a real backend
-------------------------

.. literalinclude:: /content/examples/vtc/vtc_addRunningBackend.vtc
  :language: VCL

Server - Answering more than one request
.........................................

.. literalinclude:: /content/examples/vtc/vtc_ServerReq.vtc
  :language: VCL
  :lines: 1-9

Server - Expecting a request with URL
......................................

.. literalinclude:: /content/examples/vtc/vtc_ServerReq.vtc
  :language: VCL
  :lines: 11-17


Server - Expecting particular text in body
...........................................

.. literalinclude:: /content/examples/vtc/vtc_ServerReq.vtc
  :language: VCL
  :lines: 19-31

Client - Expecting response
...........................

.. literalinclude:: /content/examples/vtc/vtc_ClientReq.vtc
  :language: VCL

Setting Varnish to expect
.........................

.. literalinclude:: /content/examples/vtc/vtc_varnishExpect.vtc
  :language: VCL


You may have noticed varnishtest preparing, executing

Some resources to look at for `VarnishTest`_
---------------------------------------------

If you want to try out the test cases available with varnishtest,
`clone`_ the `varnish-cache repository from github`_ and visit the folder:

.. code-block:: bash

  cd varnish-cache/bin/varnishtest/tests

All the tests are here. Run them with ``VarnishTest``


`Naming Scheme for VarnishTest`_

Another great post at `Smashing Magazine about VarnishTest`_ written by our
field engineer, Arianna Aondio

`Understanding VTC`_

`VarnishTest for Humans`_

`Getting started with VarnishTest`_

.. _`Getting started with VarnishTest`: https://open.blogs.nytimes.com/2016/08/29/testing-varnish-using-varnishtest/
.. _`Clone`: https://github.com/varnishcache/varnish-cache.git
.. _`varnish-cache repository from github`: https://github.com/varnishcache/varnish-cache
.. _`VarnishTest for Humans`: https://github.com/xcir/vtctrans
.. _`VarnishTest`: https://www.varnish-cache.org/docs/trunk/reference/varnishtest.html
.. _`Understanding VTC`: https://www.varnish-cache.org/docs/trunk/reference/vtc.html?highlight=varnishtest
.. _`Naming Scheme for VarnishTest`: https://raw.githubusercontent.com/varnish/Varnish-Cache/master/bin/varnishtest/tests/README
.. _`Smashing Magazine about VarnishTest`: https://www.smashingmagazine.com/2016/05/five-simple-steps-test-varnish-cache-deployment-varnishtest/
