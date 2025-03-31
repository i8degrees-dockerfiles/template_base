# compose.yml:jeff
#
# Docker compose template
#
---

networks:
  traefik-proxy:
    attachable: true
    driver: overlay
    external: true
  service-db:
  service-www:

volumes:
  data:

services:

  service-db:
    # IMPORTANT(JEFF): The version of postgres used may be specific to
    # the project you are setting up. Check with the documentation!
    # https://hub.docker.com/_/postgres
    image: postgres:15
    container_name: service-db
    expose:
      - ${SERVICE_DB_PORT:-5432}/tcp
    environment:
      - PGDATA=/var/lib/postgresql/data/pgdb
      - POSTGRES_USER=${SERVICE_DB_USER:?You must provide the 'SERVICE_DB_USER' variable in the .env file}
      - POSTGRES_PASSWORD=${SERVICE_DB_PASSWORD:?You must provide the 'SERVICE_DB_PASSWORD' variable in the .env file}
      - POSTGRES_DB=${SERVICE_DB_NAME:?You must provide the 'SERVICE_DB_NAME' variable in the .env file}
    volumes:
      # Map 'data' volume such that postgres database is stored externally
      - ${SERVICE_EXT_VOLUME:?You must specify the 'SERVICE_EXT_VOLUME' variable in the .env file!}:/var/lib/postgresql/data/:z
    restart: unless-stopped
    networks:
      - service-db

  service-server:
    env_file:
      - .env
    networks:
      - traefik-proxy
      - service-db
    expose:
      - 3000
    image: user/image:${SERVICE_TAG:-latest}
    container_name: service-server
    restart: unless-stopped
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /etc/ssl/certs:/etc/ssl/certs:ro
      - data:/data:rw

  service-proxy:
    depends_on:
      - service-server
    env_file:
      - .env
    networks:
      - traefik-proxy
    ports:
      - ${SERVICE_WEB_PORT:-3000}:80
    # https://hub.docker.com/_/nginx
    image: nginx:alpine
    container_name: service-proxy
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /etc/ssl/certs:/etc/ssl/certs:ro
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro,z
      - ${SERVICE_EXT_VOLUME}:/var/www:z
      #- static:/var/www/html/static
      #- media:/var/www/html/media
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.service-fs1.rule=Host(`service.fs1.home`) && PathPrefix(`/`)"
      - "traefik.http.routers.service-fs1.entrypoints=websecure"
      - "traefik.http.services.service-fs1.loadbalancer.server.port=80"
      - "traefik.http.routers.service-fs1.service=service-fs1"
      - "traefik.http.routers.service-fs1.tls.certresolver=staging-web-acme"
