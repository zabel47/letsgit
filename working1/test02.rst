.. role:: raw-html-m2r(raw)
   :format: html


Example
=======

This page is written in mixed markdown and reST.
Source code is `here <https://github.com/miyakogi/m2r/blob/master/docs/example.md>`_.

Basic Markups (inline)
----------------------

A **strong**\ , *emphasis*\ , :raw-html-m2r:`<del>deleted</del>`\ , ``code with single-backtick``\ ,
``code with two-backticks``\ , :raw-html-m2r:`<code class="docutils literal"><span class="pre">code can include multiple (&#96;&#96;) backticks</span></code>`\ ,
:code:`reST's code role`, and :raw-html-m2r:`<del>inline html</del>`\ delete.

Link
^^^^

Auto link to http://example.com/.

Link to `example.com <http://example.com/>`_ in markdown.

Link to `anchor <#testlabel>`_ in markdown.

Link to `document <example.md>`_ in markdown.

Link to `document with anchor <example.md#testlabel>`_ in markdown (doc directive does not support anchors, so this links to the document only).

Link to `example.com <http://example.com/>`_ in reST.

Link to `example`_ in reST_ref.

Link to :raw-html-m2r:`<a href="http://example.com/" title="example">example.com</a>` with title in markdown.

.. _example: http://example.com



.. _testlabel:


Basic Markups (block)
---------------------

This is a simple sentence.

| sentence with
| newlines
| (reST)

Sentence with\ :raw-html-m2r:`<br>`
hard-wrap (markdown, trailing two spaces)

..

   block quote
   second line

   ..

      nested quote


----


.. raw:: html

   <div style="color: red;">This is a red, raw-html block.</div>


..

   Block quote after raw-html directive


List
^^^^

Unordered list
~~~~~~~~~~~~~~


* unordered list
  new line
* next item

  * nested list
    with new line
  * nested list item 2

* original depth

  #. ordered list item
  #. second
     with new line

* original depth again

Ordered list
~~~~~~~~~~~~


#. ordered list
   in new line
#. second item

   * nested unordered list
   * second item
     with new line

#. original depth

   #. nested ordered list
      with new line
   #. again

#. original depth again

Code Block
^^^^^^^^^^

Simple, indented code block

.. code-block::

   pip install sphinx


Code block with triple backticks and language.

.. code-block:: python

   def a(n: int) -> None:
       for i in range(n):
           print(i)

Triple-tildes (\ ``~~~``\ ) are also available.

.. code-block::

   def a(n: int) -> None:
       for i in range(n):
           print(i)

Here is reST style code block.

.. code-block:: python

    if True:
        print('\n')


Extensions
----------

Table (Markdown-Style)
^^^^^^^^^^^^^^^^^^^^^^

(cell-alignment is not supported currently)

.. list-table::
   :header-rows: 1

   * - Table Header 1
     - Table Header 2
     - Table Header 3
   * - normal
     - *italic*
     - **bold**
   * - ``code``
     - :raw-html-m2r:`<del>deleted</del>`
     - :raw-html-m2r:`<b>inline-html</b>`


Math
^^^^

This is :math:`E = mc^2` inline math.

The below is math-block (markdown-style).

.. math::

   E = mc^2

The below is reST-style math-block.

.. math::

   E = mc^2



Include Markdown file
^^^^^^^^^^^^^^^^^^^^^

To include markdown file:

.. code-block:: rest

   .. mdinclude:: path-to-file.md

To include markdown file with specific lines:

.. code-block:: rest

   .. mdinclude:: included.md
      :start-line: 2
      :end-line: -2

Original ``included.md`` file is:

.. include:: included.md
   :code: md


This file included as:

.. code-block:: md

   #### Include this line

and results in HTML as below:

.. mdinclude:: included.md
   :start-line: 2
   :end-line: -2


Footnote
^^^^^^^^

Footnote\ [#fn-1]_ and footnote\ [#fn-key]_ with markdown.

Footnote with reST\ [#a]_.

:raw-html-m2r:`<!-- footnote definition -->`

.. [#a] reST footnote


.. [#fn-1] footnote 1
.. [#fn-key] footnote key
