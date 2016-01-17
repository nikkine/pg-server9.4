# pg-server9.4
This image is mainly based on the officil image of Postgres 9.4 with litle modificaition :
- Docker file :
-- Only $PG_MAJOR environment variable is used  

RUN apt-get update \
	&& apt-get install -y postgresql-common \
	&& sed -ri 's/#(create_main_cluster) .*$/\1 = false/' /etc/postgresql-common/createcluster.conf \
	&& apt-get install -y \
		postgresql-$PG_MAJOR \
		postgresql-contrib-$PG_MAJOR \
	&& rm -rf /var/lib/apt/lists/*

- docker-entrypoint.sh :
-- Those lines have been added to make non-postgres-DB owned by non-postgres-user

if ([ "$POSTGRES_DB" != 'postgres' ] && [ "$POSTGRES_USER" != 'postgres' ]); then
            		psql --username "$POSTGRES_USER" --dbname "$POSTGRES_DB" <<-EOSQL         		
				ALTER DATABASE "$POSTGRES_DB" OWNER TO "$POSTGRES_USER";
			EOSQL
            		echo
   fi
