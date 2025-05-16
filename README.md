
```mermaid

graph TD
    subgraph LinuxONE Ubuntu Host
        subgraph Docker
            db[(PostgreSQL\nMovie DB)]
            fastapi1[FastAPI App 1\n8000]
        end
        fastapi1 -->|SQL| db
    end
    
    user[User] -->|HTTP Requests| fastapi1

    classDef vm fill:#e6f3ff,stroke:#333;
    classDef container fill:#e1f5e1,stroke:#333;
    classDef user fill:#ffebee,stroke:#333;
    class LinuxONE, Docker vm;
    class db,fastapi1 container;
    class user user;

```
# Part 1 - Prerequisites
* Register at LinuxOne Community Cloud <a href="https://developer.ibm.com/linuxone/" target="_blank">here</a> for a trial account and get a Ubuntu VM.

* Click on Try Virtual Machines on the LinuxONE™ Community Cloud

![try](images/try.PNG)

# Part 2 – Setup your environment on LinuxONE VM
# Setup Guide

### Prerequisite:
- docker![alt text](https://file%2B.vscode-resource.vscode-cdn.net/Users/jazziro/codesenju/movie_lite/images/overview.png)
- jq
- make (optional)
```bash
sudo systemctl restart snapd
sudo apt update
sudo snap install docker

sudo usermod -aG docker linux1
# above changes won't apply until the user starts a new shell session
# Exit your current shell
exit
# Then log back in:
ssh -i mykey.pem linux1@<your-server>

git clone https://github.com/codesenju/nodeapi_pgmoviedb
cd nodeapi_pgmoviedb/
```
### Quick Setup:
```shell
docker compose --profile quick-setup build
# [+] Building 2/2
# ✔ fastapi    Built
# ✔ pgmoviedb  Built

docker compose --profile quick-setup up -d
# [+] Running 2/2
# ✔ Container postgres  Running 
# ✔ Container fastapi   Running

docker compose --profile quick-setup logs -f
# postgres  | title.basics.tsv imported!
# ...
# postgres  | 2025-05-16 19:26:23.344 UTC [1] LOG:  database system is ready to accept connections
```
### Test API

```shell
curl localhost:8000/api/v1/movies | jq 
```
## Visit http://\<linux1-ip\>:8000/docs