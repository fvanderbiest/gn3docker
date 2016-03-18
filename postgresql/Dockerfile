#
# Dockerfile for the database service
#

FROM debian:jessie

MAINTAINER PSC "francois.vanderbiest@camptocamp.com"

ENV DEBIAN_FRONTEND noninterative

RUN apt-get update && \
    apt-get install -y postgresql-9.4

# Run the rest of the commands as the ``postgres`` user
USER postgres

# Create a PostgreSQL role named ``geonetwork`` with ``geonetwork`` as the password and
# then create a database ``geonetwork`` owned by the ``geonetwork`` role.
RUN /etc/init.d/postgresql start && \
    psql -c "CREATE USER geonetwork WITH SUPERUSER PASSWORD 'geonetwork';" && \
    createdb -O geonetwork geonetwork && \
    /etc/init.d/postgresql stop

# Adjust PostgreSQL configuration so that remote connections to the
# database are possible. 
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/9.4/main/pg_hba.conf

# And add ``listen_addresses``
RUN echo "listen_addresses='*'" >> /etc/postgresql/9.4/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432

# Add VOLUMEs to allow backup of config, logs and databases
#~ VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]
VOLUME  ["/var/log/postgresql"]

# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/9.4/bin/postgres", "-D", "/var/lib/postgresql/9.4/main", "-c", "config_file=/etc/postgresql/9.4/main/postgresql.conf"]
