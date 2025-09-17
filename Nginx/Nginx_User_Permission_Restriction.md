# (NGINX)How to restrict user permission in NGinx location wise userbase access

code➖

server {
listen 80;
server_name 3.110.168.41;

```
# ... other configuration ...
location / {
    # Proxy the requests to the backend server running on <http://localhost:11>.
    proxy_pass <http://localhost:1234>;
    proxy_buffering off;
    proxy_connect_timeout 120;
    proxy_read_timeout 120;
    # Set necessary headers for WebSocket and proxying.
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}

# Location specifically for serving the file
location /apple-app-site-association {
    root /usr/share/nginx/well-known;
    default_type application/json;
    add_header Content-Disposition inline;
}

# Location specifically for serving the file
location /assetlinks.json {
    root /usr/share/nginx/well-known;
    default_type application/json;
    add_header Content-Disposition inline;
}
# to host text file 
location /assetlinks.txt {
    root /usr/share/nginx/well-known;
    default_type text/plain; # Set the MIME type for text files
    add_header Content-Disposition inline;
}

```

}

In above code i want to restrice location with id and password for that

eg➖

# Location block that defines the behavior for requests to this server.
    location / api {

In this nginx format i want to set username and passwordon / api how can i do this

Solution➖

To add basic authentication (username and password) to a specific location block in Nginx, you can use the **`auth_basic`** and **`auth_basic_user_file`** directives. Here's how you can set up basic authentication for the "/api" location:

## 1. **Create an Authentication File:**
Create a file to store the usernames and encrypted passwords. You can use the **`htpasswd`** utility to generate the password entries. Replace **`your_username`** with the desired username.
    
    ```bash
    bashCopy code
    sudo sh -c "echo -n 'your_username:' >> /etc/nginx/.htpasswd"
    sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"
    
    ```
    
    This will prompt you to set the password for the specified username.
    
## 2. **Configure Nginx for Basic Authentication:**
Update your Nginx configuration to include the following directives within the desired **`location`** block:
    
    ```
    nginxCopy code
    location /api {
        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/.htpasswd;
    
        # Your other configuration for /api goes here
        proxy_pass http://localhost:9001;
        proxy_buffering off;
        proxy_connect_timeout 120;
        proxy_read_timeout 120;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    ```
    
    Adjust the proxy settings and other configurations as needed within the **`/api`** location block.
    
## 3. **Restart Nginx:**
After making changes, restart Nginx to apply the new configuration:
    
    ```bash
    bashCopy code
    sudo systemctl restart nginx
    
    ```
    

Now, when someone tries to access a resource under the "/api" location, they will be prompted to enter a username and password. The credentials will be checked against the entries in the **`.htpasswd`** file.

Remember to keep the **`.htpasswd`** file secure. The **`auth_basic_user_file`** directive should point to the absolute path of the **`.htpasswd`** file.

Additionally, make sure to test the configuration and access to ensure that basic authentication is working as expected.

# For remove userbase access in Nginx file

To remove basic authentication from a specific location in Nginx, you need to do the following:

## 1. **Remove or Comment Out Auth Directives:**
Edit your Nginx configuration file and find the **`location`** block where you've set up basic authentication. Remove or comment out the **`auth_basic`** and **`auth_basic_user_file`** directives:
    
    ```
    nginxCopy code
    location /api {
        # auth_basic "Restricted Access";
        # auth_basic_user_file /etc/nginx/.htpasswd;
    
        # Your other configuration for /api goes here
        proxy_pass http://localhost:9001;
        proxy_buffering off;
        proxy_connect_timeout 120;
        proxy_read_timeout 120;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    ```
    
## 2. **Remove .htpasswd File:**
If you no longer need the **`.htpasswd`** file or if it was created specifically for this purpose, you can remove it:
    
    ```bash
    bashCopy code
    sudo rm /etc/nginx/.htpasswd
    
    ```
    
## 3. **Restart Nginx:**
After making these changes, restart Nginx to apply the updated configuration:
    
    ```bash
    bashCopy code
    sudo systemctl restart nginx
    
    ```
    

These steps will effectively remove basic authentication from the specified location in your Nginx configuration. Users will no longer be prompted for a username and password when accessing resources under the "/api" location.