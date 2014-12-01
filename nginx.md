# Nginx

App / web server with similar capabilities to Apache.

<http://nginx.org/en/docs/>

Very used by the Rails community.

## Vs apache

Architecture comparison: <http://www.thegeekstuff.com/2013/11/nginx-vs-apache>. Nginx scales better, Apache is older and has more configuration options and libraries.

Good official beginners tutorial: <http://nginx.org/en/docs/beginners_guide.html>.

## Configuration

Main configuration file on Ubuntu

    vim /etc/nginx/nginx.conf

### Serve static files

    http {
        server {
            # URL /
            location / {
                root /data/www;
            }

            # URL /images/
            location /images/ {
                root /data;
            }
        }
    }
