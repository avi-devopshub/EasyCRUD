FROM node:24-alpine

COPY . /opt/

WORKDIR /opt

RUN npm install

RUN npm run build

RUN apk add --no-cache apache2

RUN rm -rf /var/www/localhost/htdocs/*

RUN cp -r dist/* /var/www/localhost/htdocs/

# Enable Apache rewrite module
RUN sed -i 's/^#LoadModule rewrite_module/LoadModule rewrite_module/' /etc/apache2/httpd.conf

# Allow .htaccess overrides
RUN sed -i 's/AllowOverride None/AllowOverride All/' /etc/apache2/httpd.conf

# React SPA routing configuration
RUN cat > /var/www/localhost/htdocs/.htaccess <<'EOF'
RewriteEngine On

# Do not rewrite API requests
RewriteRule ^api/ - [L]

# Serve existing files and directories normally
RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^ - [L]

# Send React routes to index.html
RewriteRule ^ index.html [L]
EOF

EXPOSE 80

CMD ["httpd", "-D", "FOREGROUND"]