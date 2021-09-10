# Server config
The default.conf.template file will be used by nginx to set the server(s) config(s).
See https://hub.docker.com/_/nginx at section 'Using environment variables in nginx configuration'.
This template file has 3 placeholders whose values are defined in the ../docker-compose.yaml file.
  - NGINX_SERVER_NAME;
  - NGINX_SERVER_ROOT;
  - NGINX_FASTCGI_PASS;
