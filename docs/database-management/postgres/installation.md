# Install on linux
## Ubuntu
Use the PostgreSQL Apt Repository. This will integrate with your normal systems and patch management,
and provide automatic updates for all supported version of PostgreSQK throughout the support lifetime of PostgreSQL.

To use the apt reporitory follow hese steps:

1. Create the file repository configuration

        sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

2. Import the repository signing key

        wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

3. Update the package lists

        sudo apt-get update

4. Install the latest version of postgreSQL

        sudo apt-get -y install postgresql

Note:

        If you want a specific version, use 'postgresql-12' or similar instead of 'postgresql':
