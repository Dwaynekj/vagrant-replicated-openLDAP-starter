# Local Development

## Step 1: (Local Shell) Launch Vagrant, Replicated and OpenLDAP

- `vagrant plugin install vagrant-vbguest`
- `vagrant up replicated`

## Step 2: (Browser) Upload License, confirm settings

- Navigate to http://127.0.0.1:8800
- Upload Dev License
  - Login `https://vendor.replicated.com/apps/<your app>/customers`
  - Get License
- **use LDAP authentication** not simple auth for the first login
  - Settings should look like [OpenLDAP settings](replicated/OpenLDAP-README.md#ldap)
  - Once you save the page, it will test and confirm the LDAP setup
- Once the LDAP settings are finished, complete the remaining LRS Settings
  - Settings should look like [OpenLDAP settings](replicated/OpenLDAP-README.md#your-app-settings)
  - Confirm that that replicated successfully started and launched your app before continuing

## Step 3: (Vagrant Shell) Replicated LDAP Identity API Test

- [Docs](https://help.replicated.com/api/integration-api/identity-api/)
- `vagrant ssh replicated`
- `sudo docker ps | grep <your app>`
- `sudo docker exec -it <your container id> /bin/bash`
- Inside the container `curl -k -v $REPLICATED_INTEGRATIONAPI/identity/v1/sources` for a response
- You can also run tests from arbitrary docker containers
  - For instance, you can run random Docker containers (not replicated or managed by replicated) by explicitly attaching to the replicated docker network
    i.e. `sudo docker run -it --network=premkit_replicated centos:latest curl -k -v http://premkit_replicated:2443/ identity/v1/sources`

## Step 4: Confirm Development Cycle

- Symlink your project github folder repo to `vagrant/replicated/syncdir`
- Edit files in your local editor
- Build Docker Image _inside_ Vagrant
  - `vagrant ssh replicated`
  - `docker build ~/replicated/<your app repo>/path/to/dockerfile`
  - Reuse the same docker tags as the container currently in use. Once you restart the application from the on-prem Admin Console (http://localhost:8800) or CLI, your updated images will be used by Replicated.
  - More info from the [Replicated Docs](https://help.replicated.com/guides/iterate-with-replicated-studio/iterate/)
  
## Logging Back into the Replicated Console

- Use the uid and the password
- In this case we created a user with uid james and password "goldfinger"
- Your welcome to create more users, groups, change passwords, etc


## LDAP Examples

- Change LDAP user password `sudo docker exec my-openldap-container ldappasswd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w "admin" -s "password" "uid=james,ou=appAdmin,dc=example,dc=org"`
- LDAP Search for User`sudo docker exec my-openldap-container ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin -s sub "(uid=james)"`
- See [replicated.yml](../ansible/replicated.yml) for other ldap examples
- [EC2 Refrence Setup](https://35.175.252.194:8800/)
