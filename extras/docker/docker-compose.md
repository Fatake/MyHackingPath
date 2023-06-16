# Docker Compose

## Info

Docker Compose es una herramienta de orquestación de contenedores que permite definir y ejecutar aplicaciones multi-contenedor de manera fácil y eficiente. Con Docker Compose, podemos describir los diferentes servicios que componen nuestra aplicación en un **archivo YAML** y, a continuación, utilizar un solo comando para ejecutar y gestionar todos estos servicios de manera coordinada.

Además permite definir  múltiples contenedores en un solo archivo pudiendo incluso crear subredes de comunicación entre los mismos



{% code title="docker-compose.yml" overflow="wrap" %}
```yaml
version: "3.9"
secrets:
  public_cert:
    file: ./_secrets/fullchain.pem
  private_key:
    file: ./_secrets/privkey.pem

networks:
  reverseproxy:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.1.0/24

services:
  # Contenedor 1
  nginx:
    build: ./nginx
    image: nginx
    hostname: nginx
    networks:
     - reverseproxy
    env_file:
      - ./config.env
    volumes:
      - ./_data/config/nginx/:/etc/nginx/sites
      - ./_data/config/vhosts/:/etc/nginx/vhosts
      - ./_data/config/html:/var/www/html
    secrets:
      - source: public_cert
        target: /etc/letsencrypt/live/${DOMAIN}/fullchain.pem
      - source: private_key
        target: /etc/letsencrypt/live/${DOMAIN}/privkey.pem
    ports:
      - "80:80"
      - "443:443"
    restart: unless-stopped

  # Contenedor 2
  certbot:
    build: ./certbot
    image: certbot
    hostname: certbot
    networks:
      - reverseproxy
    env_file:
      - ./config.env
    secrets:
     - source: cloudflare_creds
       target: cloudflare_credentials.ini
    volumes:
      - ./_secrets/:/etc/letsencrypt
      - certbot_acme_challenge:/var/www/certbot


```
{% endcode %}

Ejecución

```bash
# solo build
docker-compose build
docker-compose up -d

# Buldear y poner activo 
docker-compose -f ./docker-compose.yml up --build
```





