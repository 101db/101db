#!/bin/bash

sudo add-apt-repository --remove 'deb cdrom:[Debian GNU/Linux 11.1.0 _Bullseye_ - Official i386 DVD Binary-1 20211009-10:08]/ bullseye contrib main'
sudo add-apt-repository --remove 'deb cdrom:[Debian GNU/Linux 11.1.0 _Bullseye_ - Official amd64 DVD Binary-1 20211009-10:08]/ bullseye contrib main'

sudo apt install curl gnupg2 ca-certificates lsb-release debian-archive-keyring -y
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
    | sudo tee /etc/apt/preferences.d/99nginx

sudo apt update
sudo apt install nginx -y

systemctl stop nginx

echo ' 
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<h1>hello world</h1>
</body>
</html>
' > /usr/share/nginx/html/index.html

systemctl start nginx
