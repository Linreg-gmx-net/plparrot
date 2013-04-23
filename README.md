# PL/Parrot

PL/Parrot is a PostgreSQL procedural language for the Parrot Virtual Machine.


# Installation

For the impatient, to install PL/Parrot into a PostgreSQL database, just do this:

    make
    make install
    make installcheck

If you get an error such as:

Makefile:13: /usr/lib/postgresql/8.4/lib/pgxs/src/makefiles/pgxs.mk: No such file or directory
make: *** No rule to make target `/usr/lib/postgresql/8.4/lib/pgxs/src/makefiles/pgxs.mk'.  Stop.

then you probably installed postgres from a debian package. You need to install the
postgresql-server-dev-X package, where X is your version of PostgreSQL. For example:

    sudo apt-get install postgresql-server-dev-8.4

To run the tests with pg_prove you should create the plpgsql language in your database.
The default database name is your current username, so this should work:

    createlang plpgsql $USER
    make test

If you encounter an error such as:

    "Makefile", line 8: Need an operator

You need to use GNU make, which may well be installed on your system as
'gmake':

    gmake
    gmake install
    gmake installcheck

If you encounter an error such as:

    make: pg_config: Command not found

Be sure that you have `pg_config` installed and in your path. If you used a
package management system such as RPM to install PostgreSQL, be sure that the
`-devel` package is also installed. If necessary, add the path to `pg_config`
to your `$PATH` environment variable:

    env PATH=$PATH:/path/to/pgsql/bin \
    make && make install && make installcheck

And finally, if all that fails, copy the entire distribution directory to the
`contrib/` subdirectory of the PostgreSQL source tree and try it there without
the `$USE_PGXS` variable:

    make NO_PGXS=1
    make install NO_PGXS=1
    make installcheck NO_PGXS=1

If you encounter an error such as:

    ERROR:  must be owner of database regression

You need to run the test suite using a super user, such as the default
"postgres" super user:

    make installcheck PGUSER=postgres

# Testing PL/Parrot with PL/Parrot

In addition to the PostgreSQL-standard `installcheck` target, the `test`
target uses the `pg_prove` Perl program to do its testing, which requires
TAP::Harness, included in
[Test::Harness](http://search.cpan.org/dist/Test-Harness/ "Test::Harness on
CPAN") 3.x. You'll need to make sure that you use a database with PL/pgSQL
loaded, or else the tests won't work. `pg_prove` supports a number of
environment variables that you might need to use, including all the usual
PostgreSQL client environment variables:

* `$PGDATABASE`
* `$PGHOST`
* `$PGPORT`
* `$PGUSER`

You can use it to run the test suite as a database super user like so:

    make test PGUSER=postgres

Of course, if you're running the tests from the `contrib/` directory, you
should add the `NO_PGXS` variable.

# Adding PL/Parrot to a Database

Once PL/Parrot has been built and tested, you can install it into a
PL/pgSQL-enabled database:

    psql -d dbname -f plparrot.sql

If you want PL/Parrot to be available to all new databases, install it into the
"template1" database:

    psql -d template1 -f plparrot.sql

The `plparrot.sql script will also be installed in the `contrib` directory
under the directory output by `pg_config --sharedir`. So you can always do
this:

    psql -d template1 -f `pg_config --sharedir`/contrib/plparrot.sql

# License

This code is distributed under the terms of the Artistic License 2.0.
For more details, see the full text of the license in the file LICENSE.
