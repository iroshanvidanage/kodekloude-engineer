# Temporary User Setup with Expiry

### Task

A developer John has been assigned Nautilus project temporarily as a backup resource. As a temporary resource for this project, we need a temporary user for John. It’s a good idea to create a user with a set expiration date so that the user will not be able to access servers beyond that point.

Therefore, create a user named john on the App Server 2. Set expiry date to 2021-12-07 in Stratos Datacenter. Make sure the user is created as per standard and is in lowercase.


---

### Solution

1. Login to the app server and verify

```sh

# switch to root
sudo -i

# check user is present
id john

# create user
useradd -e 2021-07-12 john

```

2. Verify the user creation

```sh

# check user creation
id john

# check expiry
chage -l john

```
