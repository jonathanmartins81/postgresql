# Install PostgreSQL on Manjaro

Source: https://wiki.archlinux.org/index.php/PostgreSQL

1. Specification-OS
```bash
lsb_release -cd ; getconf LONG_BIT ; lsb_release -a
```

2. Install postgresql package
`sudo pacman -Sy ; sudo pacman -S postgresql ; postgres --version`

3. Switch to the postgres user account and initialize the database cluster:
```bash
sudo -iu postgres
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data/'
exit
```

**Options for initdb are as follows:**

- `--locale` is the one defined in /etc/locale.conf.
- `-E` is the default encoding for new databases.
- `-D` is the default location for storing the database cluster.

4. Start and enable the postgresql service

`sudo systemctl enable --now postgresql.service`

5. Create a Database and Database User

`sudo su - postgres`

You should now be in a shell session for the postgres user. Log into a Postgres session by typing:

`psql`

First, we will create a database for our Django project. Each project should have its own isolated database for security reasons. We will call our database myproject in this guide, but it’s always better to select something more descriptive:

`CREATE DATABASE myproject;`

Next, we will create a database user which we will use to connect to and interact with the database. Set the password to something strong and secure:

`CREATE USER myprojectuser WITH PASSWORD 'password';`

Afterwards, we’ll modify a few of the connection parameters for the user we just created. This will speed up database operations so that the correct values do not have to be queried and set each time a connection is established.

We are setting the default encoding to UTF-8, which Django expects. We are also setting the default transaction isolation scheme to “read committed”, which blocks reads from uncommitted transactions. Lastly, we are setting the timezone. By default, our Django projects will be set to use UTC:

```bash
ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE myprojectuser SET timezone TO 'UTC';
```

Now, all we need to do is give our database user access rights to the database we created:

`GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;`

Exit the SQL prompt to get back to the postgres user’s shell session:

`\q`

Exit out of the postgres user’s shell session to get back to your regular user’s shell session:

`exit`

## Django Connection

Install the package to connect to our DB through Django

`pip install django psycopg2`

On `settings.py` we need to change the driver and configure the credentials to connect to our PostgreSQL DB:

```python
	DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.postgresql_psycopg2',
          'NAME': 'myproject',
          'USER': 'myprojectuser',
          'PASSWORD': 'password',
          'HOST': 'localhost',
          'PORT': '',
      }
  }
```

## Django testing
For testing, Django will try to create a test database with the **USER** set in `settings.py` (in this case *myprojectuser*)

In this case you need to give permission to create databases to that user.

```bash
# Log as postgres user and execute psql
sudo su - postgres

psql

\du # List all roles with his permissions

ALTER USER myprojectuser CREATEDB # Also can be used with ALTER ROLE (See Postgres docs)

\du # Should list the new permission

\q

exit
```