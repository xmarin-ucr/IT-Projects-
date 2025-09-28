## SSH Configuration

## Pasos para que permita llaves y contraseñas 

Steps to Enable Key and Password Authentication

Use the following command to access the sshd_config file:

``` 
sudo nano /etc/ssh/sshd_config
``` 

This command is executed to modify the SSH configuration—for example, to allow both key-based and password authentication.

* Locate the line that says PasswordAuthentication, uncomment it, and ensure the value is set to yes.

``` 
PasswordAuthentication yes
``` 

* This allows the SSH server to authenticate users using both public/private key pairs and passwords. 

Similarly, uncomment the line PubKeyAuthentication. This enables the use of SSH key pairs, which can save time when accessing Rocky Linux servers remotely and executing commands.

``` 
PubKeyAuthentication yes
``` 

The command is to restart: 
``` 
sudo systemctl restart sshd
``` 

### Generación de llaves en SSH

To create an SSH key pair from a Windows terminal, run:
``` 
ssh-keygen
``` 

This step is not part of the Golden Image or hardening process, so it is not covered in detail in this report.

For more information, refer to the guide:[SSH Key Configuration on Rocky Linux](https://www-digitalocean-com.translate.goog/community/tutorials/how-to-set-up-ssh-keys-on-rocky-linux-9?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=tc&_x_tr_hist=true)


## Disable Root Login via SSH

To prevent the root user from logging in via SSH, access the configuration file:

``` 
sudo nano /etc/ssh/sshd_config
``` 

Uncomment the line PermitRootLogin and set its value to no. This ensures administrators must authenticate with their individual accounts before escalating privileges to root.

``` 
PermitRootLogin no
``` 

## Prevent Empty Passwords

To block login attempts with empty passwords, access the configuration file:

``` 
sudo nano /etc/ssh/sshd_config
``` 

Uncomment the line PermitEmptyPasswords and set it to no. This reduces the risk of unauthorized access and strengthens system security.

``` 
PermitEmptyPasswords no
``` 