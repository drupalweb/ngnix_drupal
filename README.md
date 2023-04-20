server {
	server_name apidms-bisco.narvanapps.ir www.apidms-bisco.narvanapps.ir mail.apidms-bisco.narvanapps.ir;
	listen 193.151.136.205;
	root /home/narvanapps/domains/apidms-bisco.narvanapps.ir/public_html/dms/web;
	index index.php index.htm index.html;
	access_log /var/log/virtualmin/apidms-bisco.narvanapps.ir_access_log;
	error_log /var/log/virtualmin/apidms-bisco.narvanapps.ir_error_log;

	fastcgi_buffers 16 16k; 
	fastcgi_buffer_size 32k;

    gzip on;
    gzip_comp_level 6;
    gzip_vary on;
    gzip_min_length 1000;
    gzip_proxied expired no-cache no-store private auth;
    gzip_types text/plain application/javascript application/x-javascript text/javascript text/xml text/css;
    gzip_disable "MSIE [1-6]\.";
    client_max_body_size 100M;

	fastcgi_param GATEWAY_INTERFACE CGI/1.1;
	fastcgi_param SERVER_SOFTWARE nginx;
	fastcgi_param QUERY_STRING $query_string;
	fastcgi_param REQUEST_METHOD $request_method;
	fastcgi_param CONTENT_TYPE $content_type;
	fastcgi_param CONTENT_LENGTH $content_length;
	fastcgi_param SCRIPT_FILENAME "/home/narvanapps/domains/apidms-bisco.narvanapps.ir/public_html/dms/web$fastcgi_script_name";
	fastcgi_param SCRIPT_NAME $fastcgi_script_name;
	fastcgi_param REQUEST_URI $request_uri;
	fastcgi_param DOCUMENT_URI $document_uri;
	fastcgi_param DOCUMENT_ROOT /home/narvanapps/domains/apidms-bisco.narvanapps.ir/public_html/dms/web;
	fastcgi_param SERVER_PROTOCOL $server_protocol;
	fastcgi_param REMOTE_ADDR $remote_addr;
	fastcgi_param REMOTE_PORT $remote_port;
	fastcgi_param SERVER_ADDR $server_addr;
	fastcgi_param SERVER_PORT $server_port;
	fastcgi_param SERVER_NAME $server_name;
	fastcgi_param PATH_INFO $fastcgi_path_info;
	fastcgi_param HTTPS $https;
	location ^~ /.well-known/ {
		try_files $uri /;
	}
	location ~ "\.php(/|$)" {
		try_files $uri $fastcgi_script_name =404;
		default_type application/x-httpd-php;
		fastcgi_pass unix:/var/php-nginx/167583792331208.sock/socket;
	}
	fastcgi_split_path_info "^(.+\.php)(/.+)$";
	location /cgi-bin/ {
		gzip on;
		root /home/narvanapps/domains/apidms-bisco.narvanapps.ir/cgi-bin;
		fastcgi_pass unix:/var/fcgiwrap/167583792331208.sock/socket;
		fastcgi_param SCRIPT_FILENAME "/home/narvanapps/domains/apidms-bisco.narvanapps.ir$fastcgi_script_name";
		fastcgi_param GATEWAY_INTERFACE CGI/1.1;
		fastcgi_param SERVER_SOFTWARE nginx;
		fastcgi_param QUERY_STRING $query_string;
		fastcgi_param REQUEST_METHOD $request_method;
		fastcgi_param CONTENT_TYPE $content_type;
		fastcgi_param CONTENT_LENGTH $content_length;
		fastcgi_param SCRIPT_NAME $fastcgi_script_name;
		fastcgi_param REQUEST_URI $request_uri;
		fastcgi_param DOCUMENT_URI $document_uri;
		fastcgi_param DOCUMENT_ROOT /home/narvanapps/domains/apidms-bisco.narvanapps.ir/public_html/dms/web;
		fastcgi_param SERVER_PROTOCOL $server_protocol;
		fastcgi_param REMOTE_ADDR $remote_addr;
		fastcgi_param REMOTE_PORT $remote_port;
		fastcgi_param SERVER_ADDR $server_addr;
		fastcgi_param SERVER_PORT $server_port;
		fastcgi_param SERVER_NAME $server_name;
		fastcgi_param PATH_INFO $fastcgi_path_info;
		fastcgi_param HTTPS $https;
	}
	location = /favicon.ico {
    log_not_found off;
    access_log off;
  }

  location = /robots.txt {
    allow all;
    log_not_found off;
    access_log off;
  }

  # Very rarely should these ever be accessed outside of your lan
  location ~* \.(txt|log)$ {
    allow 192.168.0.0/16;
    deny all;
  }

  location ~ \..*/.*\.php$ {
    return 403;
  }

  location ~ ^/sites/.*/private/ {
    return 403;
  }

  # Allow "Well-Known URIs" as per RFC 5785
  location ~* ^/.well-known/ {
    allow all;
  }

  # Block access to "hidden" files and directories whose names begin with a
  # period. This includes directories used by version control systems such
  # as Subversion or Git to store control files.
  location ~ (^|/)\. {
    return 403;
  }

  location / {
    # try_files $uri @rewrite; # For Drupal <= 6
    try_files $uri /index.php?$query_string; # For Drupal >= 7
  }

  location @rewrite {
    rewrite ^/(.*)$ /index.php?q=$1;
  }

  # Don't allow direct access to PHP files in the vendor directory.
  location ~ /vendor/.*\.php$ {
    deny all;
    return 404;
  }
  # Fighting with Styles? This little gem is amazing.
  # location ~ ^/sites/.*/files/imagecache/ { # For Drupal <= 6
  location ~ ^/sites/.*/files/styles/ { # For Drupal >= 7
    try_files $uri @rewrite;
  }

  # Handle private files through Drupal. Private file's path can come
  # with a language prefix.
  location ~ ^(/[a-z\-]+)?/system/files/ { # For Drupal >= 7
    try_files $uri /index.php?$query_string;
  }

  location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
    try_files $uri @rewrite;
    expires max;
    log_not_found off;
  }
	listen 193.151.136.205:443 ssl;
	ssl_certificate /etc/ssl/virtualmin/167583792331208/ssl.combined;
	ssl_certificate_key /etc/ssl/virtualmin/167583792331208/ssl.key;
}
