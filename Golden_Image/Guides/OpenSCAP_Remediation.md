### Failed Result 1: 
```
--- Starting Remediation ---
Title   Ensure all users last password change date is in the past 
Rule    xccdf_org.ssgproject.content_rule_accounts_password_last_change_is_in_past
Result  fail
```
### Solution 1:
This failure indicates that some users have password change dates set in the future.

To check the current password:

```
chage -l admin
```
```
sudo chage -l root
```

To correct the date and ensure future scans pass:

```
sudo chage --lastday 2025-08-29 root
sudo chage --lastday 2025-08-29 admin
```

### Failed Result 2: 
```
Title   Set Boot Loader Password in grub2 
Rule xccdf_org.ssgproject.content_rule_grub2_password
Result fail  
```

### Solution 2:
```
sudo grub2-setpassword
```

Creates an encrypted password and ensures the GRUB2 boot loader is configured. 

### Failed Result 3: 
```
Title   Limit Users' SSH Access 
Rule    xccdf_org.ssgproject.content_rule_sshd_limit_user_access
Result fail
```

### Solution 3:
Edit the SSH configuration file:
```   
sudo nano /etc/ssh/sshd_config
```

Add the following line to restrict SSH access to the admin user only:

```
AllowUsers admin
```