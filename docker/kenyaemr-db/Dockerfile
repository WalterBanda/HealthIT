# Get Mysql DB base image
FROM mysql/mysql-server:latest

# Download the blank sql and other setup files
RUN curl -o /docker-entrypoint-initdb.d/blank_openmrs.sql https://doc-04-0s-docs.googleusercontent.com/docs/securesc/jf7akb1jamo3c8fe0bq8nj8o453kqbf7/biqdab50u1v7rh9a0om7nqndv5f8js8p/1674889875000/01074820708144119444/06645273130455365593Z/14rlO3sG9yTL9igAN8xbVqKfctyuemDN8?e=download&uuid=722cca23-3cd9-46ef-a625-857941c3b0aa&nonce=kq281t3gq5l5q&user=06645273130455365593Z&hash=7cnetb52tdt3ma2pmvnj3lk0su7tlbcm

# Entrypoint for mysql 
ENTRYPOINT [ "/entrypoint.sh" ]