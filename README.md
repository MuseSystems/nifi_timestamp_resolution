# Preserving Database Timestamp Resolution in Apache NiFi

This repository holds source code which accompanies the blog article
[Preserving Database Timestamp Resolution in Apache NiFi](https://muse.systems/blog/nifi-timestamp-resolution).

## Requirements

The requirements to run the software in this repository are:

  * PostgreSQL (versions 12 through 16)

  * Apache NiFi 2.0.0-M2

I expect that later versions of these tools will also work, but you never
know.  Naturally, PostgreSQL & Apache NiFi both have their own requirements
which will also have to be satisfied; please refer to the documentation of
those products for their individual requirements:

  * [PostgreSQL](https://www.postgresql.org/docs/16/tutorial-start.html)

  * [Apache NiFi](https://nifi.apache.org/documentation/nifi-2.0.0-M2/html/administration-guide.html)

Finally, we assume you have a good working knowledge of PostgreSQL, Apache
NiFi, and how to perform basic administration tasks with these tools.  The
scope of the blog article and this repository is simply to show a way to get
full timestamp resolution in database flow file processing.

## Repository Files

The files provided by this repository include:

  1. __database.psql__

     A [psql](https://www.postgresql.org/docs/16/app-psql.html) script which will create a sample database and data.

  2. __flow.conf.gz__

     An Apache NiFi data flow definition.

  3. __cleanup.psql__

     A [psql](https://www.postgresql.org/docs/16/app-psql.html) script which will cleanup what `database.psql` created.

## Setup Notes

The following notes should help you use the code in this repository to set up
the examples.

### Database Setup

We assume that you have a local PostgreSQL server cluster up and running and
on which you have at least create database permissions.  The included psql
script may work on servers or cloud managed PostgreSQL databases, but we have
not tested or attempted this.

  1. Get this software repository (clone it).

  2. Execute the `database.psql` script using psql.  If you're installing it
     on a local instance of PostgreSQL what you should enter should be close
     to:

     `psql postgresql://localhost/postgres -f database.psql`

The script will create the database, the three tables used in the example
flows, and it will populate the `source_table` table with some data.  On my
system the script runs in about 5 seconds; the script inserts records with a
random delay between records to get some nice timestamps for our
demonstration.

When finished with the example, to clean up and drop the database instance
simply execute:

```
psql postgresql://localhost/postgres -f cleanup.psql
```

Or just drop the database manually.

### Apache NiFi Setup

These are the basic steps for Apache NiFi:

  1. Download Apache NiFi 2.0.0-M2 and extract it to the appropriate directory
     on you computer.

  2. Follow the basic Apache NiFi setup and installation instructions as found
     in their documentation.

  3. In the `conf/nifi.properties` file, set the `nifi.sensitive.props.key`
     value to: `muse.totally.insecure.password.demo.use.only`. Once done that
     entry should read:

     `nifi.sensitive.props.key=muse.totally.insecure.password.demo.use.only`

  3. Install the PostgreSQL JDBC driver into the Apache NiFi `lib` directory.

  4. Copy the `flow.json.gz` file in this repository to the Apache NiFi `conf`
     directory.

  5. Start Apache Nifi and log in.

  6. Set the `DBCPConnectionPool` controller service's database username and
     password properties to values appropriate for your database server.

Once this is done the demonstration should be ready to run using the Apache
NiFi user interface.
