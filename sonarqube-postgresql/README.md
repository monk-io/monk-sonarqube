# Mattermost-enterprise  & Monk

This repository contains Monk.io template to deploy sonarqube-community system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template includes Nginx as a reverse proxy and PostgreSQL a sonarqube-community out of box.

## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Clone Monk Mattermost-enterprise repository

In order to load templates and change configuration simply use below commands: 
```bash
git clone https://github.com/kaganmersin/monk-mattermost.git

# and change directory to the mattermost-enterprise template folder
cd mattermost-enterprise
```


## Configuration

You can add/remove configuration of the template.

The current variables can be found in `mattermost-enterprise/stack/variables` section

```yaml
  variables:
    defines: variables
    database-user:
      type: string
      value: "mattermost"
    database-password:
      type: string
      value: "password"
    database-name:
      type: string
      value: "mattermost"
    TZ:
      env: TZ
      type: string
      value: "UTC"
    mattermost-server-name:
      type: string
      value: mm.example.com
    mattermost-image-tag: 
       type: string
       value: "latest"
    nginx-listen-port:
      type: int
      value: 8080
    nginx-image-tag: 
       type: string
       value: "latest"        
```

##  Template variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **database-user** | Postgresql database username that  used by mattermost | string | mattermost
| **database-password** | Postgresql database username password that used by mattermost | string | password
| **database-name** | Postgresql database name that  used by mattermost | string | mattermost
| **TZ** | Timezone | string | UTC 
| **mattermost-server-name** | Fqdn that nginx will accept and route to. | string | mm.example.com |
| **mattermost-image-tag** | Mattermost-preview image version. | string | latest |
| **nginx-listen-port** | Configures the ports that the nginx listens on. | int | 8081 |
| **nginx-image-tag** | Nginx image version. | string | latest |



## Local Deployment

First clone the repository and change the current directory to the /mattermost-enterprise folder and simply run below command after launching `monkd`:
:

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 mattermost-enterprise/mattermost
 │  ├─🧩 mattermost-enterprise/database
 │  └─🧩 mattermost-enterprise/nginx
 └─🔗 Process groups:
    └─🧩 mattermost-enterprise/stack
✔ All templates loaded successfully

➜  monk list mattermost-enterprise

✔ Got the list
Type      Template                          Repository             Version      Tags
runnable  mattermost-enterprise/database    local                  12           dataops, database
runnable  mattermost-enterprise/mattermost  local                  -            -
runnable  mattermost-enterprise/nginx       local                  -            -
group     mattermost-enterprise/stack       local                  -            -
runnable  nginx/latest                      mattermost-enterprise  -            -
runnable  nginx/reverse-proxy               mattermost-enterprise  -            -
runnable  nginx/reverse-proxy-ssl-certbot   mattermost-enterprise  1.15-alpine  nginx, reverse proxy, ssl, certbot
➜  monk run mattermost-enterprise/stack

✔ Started mattermost-enterprise/stack
```

This will start the entire mattermost-enterprise/stack  with a Nginx reverse proxy and Postgresql. 

To access mattermost-enterprise from local system, required  dns entry needs to be added in local host file as following format: 

```
 127.0.0.1 <mattermost-server-name>
```

## Cloud Deployment

To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.

```bash
➜  monk cluster new
? New cluster name mattermost-preview
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add -p gcp -f <path/to/your-key.json>
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Cloud provider gcp
? Name of the new instance my-instance
? Tags (split by whitespace) mattermost
? Region europe-central2
? Zone europe-central2-a
? Instance type e2-medium
? Number of instances (or press ENTER to use default = 1) 3
? Default disk type for gcp is HDD (pd-standard). Would you like to change it? No
? Disk size (or press ENTER to use default = 250 GBs) 50
✔ Start creation of new instance(s) on gcp... DONE
✔ Creating node: my-instance-1 DONE
✔ Initializing node: my-instance-1 DONE
✔ Creating node: my-instance-2 DONE
✔ Initializing node: my-instance-2 DONE
✔ Creating node: my-instance-3 DONE
✔ Initializing node: my-instance-3 DONE
✔ Connecting: my-instance-1 DONE
✔ Syncing peer: my-instance-1 DONE
✔ Connecting: my-instance-2 DONE
✔ Connecting: my-instance-3 DONE
✔ Syncing peer: my-instance-2 DONE
✔ Syncing peer: my-instance-3 DONE
✔ Syncing nodes DONE
✔ Cluster grown successfully
```

Once cluster is ready execute the same command as for local and select your cluster (the option will appear automatically).

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 mattermost-enterprise/mattermost
 │  ├─🧩 mattermost-enterprise/database
 │  └─🧩 mattermost-enterprise/nginx
 └─🔗 Process groups:
    └─🧩 mattermost-enterprise/stack
✔ All templates loaded successfully
➜  monk list mattermost-preview

✔ Got the list
Type      Template                          Repository             Version      Tags
runnable  mattermost-enterprise/database    local                  12           dataops, database
runnable  mattermost-enterprise/mattermost  local                  -            -
runnable  mattermost-enterprise/nginx       local                  -            -
group     mattermost-enterprise/stack       local                  -            -
runnable  nginx/latest                      mattermost-enterprise  -            -
runnable  nginx/reverse-proxy               mattermost-enterprise  -            -
runnable  nginx/reverse-proxy-ssl-certbot   mattermost-enterprise  1.15-alpine  nginx, reverse proxy, ssl, certbot

➜  monk run mattermost-enterprise/stack

✔ Started mattermost-enterprise/stack
```
## Logs & Shell

```bash
# show Mattermost-preview logs
➜  monk logs -l 1000 -f mattermost-enterprise/mattermost

# show Nginx logs
➜  monk logs -l 1000 -f mattermost-enterprise/nginx

# show Postgresql logs
➜  monk logs -l 1000 -f mattermost-enterprise/database

# access shell in the container running Mattermost
➜  monk shell mattermost-enterprise/mattermost

# access shell in the container running Nginx
➜  monk shell mattermost-enterprise/nginx

# access shell in the container running Postgresql
➜  monk shell mattermost-enterprise/database
```

## Stop, remove and clean up workloads and templates

```bash
➜ monk purge -x mattermost-enterprise/stack mattermost-enterprise/mattermost mattermost-enterprise/nginx mattermost-enterprise/database

✔ mattermost-enterprise/stack purged
✔ mattermost-enterprise/mattermost purged
✔ mattermost-enterprise/nginx purged
✔ mattermost-enterprise/database purged
```