# sonarqube-postgresql  & Monk

This repository contains Monk.io template to deploy sonarqube-community system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template includes Nginx as a reverse proxy and PostgreSQL a sonarqube-community out of box.

## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Clone Monk sonarqube-postgresql repository

In order to load templates and change configuration simply use below commands: 
```bash
git clone https://github.com/kaganmersin/monk-mattermost.git

# and change directory to the sonarqube-postgresql template folder
cd sonarqube-postgresql
```


## Configuration

You can add/remove configuration of the template.

The current variables can be found in `sonarqube-postgresql/stack/variables` section

```yaml
  variables:
    defines: variables
    database-user:
      type: string
      value: "sonarqube"
    database-password:
      type: string
      value: "password"
    database-name:
      type: string
      value: "sonarqube"
    nginx-listen-port:
      type: int
      value: 8080
    nginx-image-tag: 
       type: string
       value: "latest" 
    sonarqube-server-name:
      type: string
      value: sonarqube.example.com 
    sonarqube-image-tag:
      type: string
      value: lts-community      
```

##  Template variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **database-user** | Postgresql database username that  used by mattermost | string | sonarqube
| **database-password** | Postgresql database username password that used by sonarqube | string | password
| **database-name** | Postgresql database name that  used by mattermost | string | sonarqube
| **TZ** | Timezone | string | UTC 
| **sonarqube-server-name** | Fqdn that nginx will accept and route to. | string | sonarqube.example.com |
| **sonarqube-image-tag** | Mattermost-preview image version. | string | latest |
| **nginx-listen-port** | Configures the ports that the nginx listens on. | int | 8081 |
| **nginx-image-tag** | Nginx image version. | string | lts-community |


## Docker Host Requirements

Because SonarQube uses an embedded Elasticsearch, make sure that your Docker host configuration complies with the [Elasticsearch production mode requirements](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-cli-run-prod-mode) and [File Descriptors configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/file-descriptors.html).

For example, on Linux, you can set the recommended values for the current session by running the following commands as root on the host:

```bash
sysctl -w vm.max_map_count=524288
sysctl -w fs.file-max=131072
ulimit -n 131072
ulimit -u 8192
```




## Local Deployment

First clone the repository and change the current directory to the /sonarqube-postgresql folder and simply run below command after launching `monkd`:
:

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 sonarqube-postgresql/database
 │  ├─🧩 sonarqube-postgresql/nginx
 │  └─🧩 sonarqube-postgresql/sonarqube
 └─🔗 Process groups:
    └─🧩 sonarqube-postgresql/stack
✔ All templates loaded successfully

➜  monk list sonarqube-postgresql

✔ Got the list
Type      Template                         Repository            Version      Tags
runnable  nginx/latest                     sonarqube-postgresql  -            -
runnable  nginx/reverse-proxy              sonarqube-postgresql  -            -
runnable  nginx/reverse-proxy-ssl-certbot  sonarqube-postgresql  1.15-alpine  -
runnable  sonarqube-postgresql/database    local                 12           dataops, database
runnable  sonarqube-postgresql/nginx       local                 -            -
runnable  sonarqube-postgresql/sonarqube   local                 -            -
group     sonarqube-postgresql/stack       local                 -            -
➜  monk run sonarqube-postgresql/stack

✔ Started local/sonarqube-postgresql/stack

```

This will start the entire sonarqube-postgresql/stack  with a Nginx reverse proxy and Postgresql. 

To access sonarqube-postgresql from local system, required  dns entry needs to be added in local host file as following format: 

```
 127.0.0.1 <sonarqube-server-name>
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
 │  ├─🧩 sonarqube-postgresql/database
 │  ├─🧩 sonarqube-postgresql/nginx
 │  └─🧩 sonarqube-postgresql/sonarqube
 └─🔗 Process groups:
    └─🧩 sonarqube-postgresql/stack
✔ All templates loaded successfully

➜  monk list sonarqube-postgresql

✔ Got the list
Type      Template                         Repository            Version      Tags
runnable  nginx/latest                     sonarqube-postgresql  -            -
runnable  nginx/reverse-proxy              sonarqube-postgresql  -            -
runnable  nginx/reverse-proxy-ssl-certbot  sonarqube-postgresql  1.15-alpine  -
runnable  sonarqube-postgresql/database    local                 12           dataops, database
runnable  sonarqube-postgresql/nginx       local                 -            -
runnable  sonarqube-postgresql/sonarqube   local                 -            -
group     sonarqube-postgresql/stack       local                 -            -
➜  monk run sonarqube-postgresql/stack

✔ Started local/sonarqube-postgresql/stack

```

## Logs & Shell

```bash
# show Mattermost-preview logs
➜  monk logs -l 1000 -f sonarqube-postgresql/sonarqube

# show Nginx logs
➜  monk logs -l 1000 -f sonarqube-postgresql/nginx

# show Postgresql logs
➜  monk logs -l 1000 -f sonarqube-postgresql/database

# access shell in the container running Mattermost
➜  monk shell sonarqube-postgresql/sonarqube

# access shell in the container running Nginx
➜  monk shell sonarqube-postgresql/nginx

# access shell in the container running Postgresql
➜  monk shell sonarqube-postgresql/database
```

## Stop, remove and clean up workloads and templates

```bash
➜ monk purge -x sonarqube-postgresql/stack sonarqube-postgresql/sonarqube sonarqube-postgresql/nginx sonarqube-postgresql/database

✔ sonarqube-postgresql/stack purged
✔ sonarqube-postgresql/sonarqube purged
✔ sonarqube-postgresql/nginx purged
✔ sonarqube-postgresql/database purged
```