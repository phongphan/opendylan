Adding New Libraries
====================

.. note:: There is a new package system and ``dylan`` tool packaged with the
          Open Dylan release which provides an alternative to using Git
          submodules. It automatically downloads dependencies, creates registry
          files for you, and uses Semantic Versioning to specify
          dependencies. See the `dylan-tool`_ docs for more information.

Adding a Git Submodule
----------------------

One way of handling inter-library dependencies is to use git submodules. This
allows you to specify a precise version that you rely upon, but assumes that
we're all using git.

We tend to keep all git submodules in a top level directory
within the repository named ``ext``. To add a new submodule::

  git submodule add <repository URL> ext/<name>

The *repository URL* should be a publicly accessible URL, so
it is recommended to use either the git or https protocols
(``git://`` or ``https://``) rather than SSH (``git@``).

The ``name`` should be the name of the repository.

For example, to add the ``tracing`` library as a submodule::

  git submodule add https://github.com/dylan-foundry/tracing.git ext/tracing

Updating a Git Submodule
------------------------

If the submodule has been updated to point at a new revision, after
you do a ``git pull``, update your submodules with::

  git submodule update --init --recursive

If you want to update the submodule to point to a new revision::

  cd ext/<name>
  git pull --ff-only origin main  # (or master)
  cd ../..
  git add ext/<name>
  git commit -m 'Updated <name> to version xxx.'

Setting Up Registry Entries
---------------------------

For each library that you add as a submodule, create a registry entry so that
the Open Dylan compiler can find the library.  See :doc:`source-registries` for
more detail.

For example, to create a registry entry for the tracing library::

  echo abstract://dylan/ext/tracing/tracing-core/tracing-core.lid \
    > registry/generic/tracing-core

You can usually get a good idea for what registry entries are
needed by looking into the registry directory of the library
that you're using.

Transitive Dependencies
-----------------------

The Dylan compiler won't find transitive dependencies, so you
will need to create registry entries for them as well.

Sometimes, you will want to create git submodules for them as
well, but other times you can just reference them from the
version that was pulled in with the existing submodule.

As an example, if you pull in the HTTP library, it has
a number of submodules, so you don't need to pull each
of those in directly, but can reference them through
the ``ext/http/`` directory. (Note in this case that
the ``http`` library uses a non-standard name for the
directory holding its submodules.)

.. _dylan-tool: https://docs.opendylan.org/packages/dylan-tool/documentation/source/index.html
