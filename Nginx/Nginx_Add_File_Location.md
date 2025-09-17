# NGINX add file location

login to server

go to location cd /etc/nginx/sites-available/

read file which you want to edit with (nano) command.

in file add below code for json file format

<aside>
 # Location specifically for serving the file
location /test.json {
    root /usr/share/nginx/well-known;
    default_type application/json;
    add_header Content-Disposition inline;
}

</aside>

in location line /test.json➖ is a file name add your file name here.

in root /usr/share/nginx/well-known; ➖this is a file location where your file is saved this loaction in depends on your location

- default_type application/json; ➖ this is for json file format

if you want to add .txt file then use below code

<aside>
 # Location specifically for serving the file
location /test.txt {
    root /usr/share/nginx/well-known;
    default_type text/plain;
    add_header Content-Disposition inline;
}

</aside>

- default_type text/plain; :- this is for txt file format