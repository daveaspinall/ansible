# ansible
My Ansible playbook

## Setup

Install Ansible via brew:

```
brew install ansible
```

Add your server IP (external) and SSH user to the `hosts` file:

```
[google-webservers]
12.34.56.78

ansible_ssh_user=your-ssh-user
```

Create a `.conf` file for your site inside `vhosts/files/sites`, example here (changing `website.com` to your required domain):

```
server {
    listen 80;

    root /var/www/website.com;
    index index.html index.htm index.php;

    server_name website.com www.website.com;

    location ~ \.php$ {
        try_files $uri =404;
        include /etc/nginx/fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_intercept_errors on;
    }

    ## Wordpress Rewrite
    if (!-e $request_filename) {
        rewrite ^(.*)$ /index.php?q=$1 last;
        break;
    }
}
```

Then add the site you just set up to `vhosts/vars/main.yml` under `enabledSites`:

```
---
enabledSites:
  - website.com.conf
disabledSites:
  - example.com.conf
```

## Running the playbook

To run the playbook on all hosts use the following command:

```
ansible-playbook webservers.yml -i hosts
```
