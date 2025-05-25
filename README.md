
```mermaid

graph TD
    User[External User] -->|HTTP :8000| FastAPI
    subgraph LinuxONE Ubuntu
        subgraph Docker
            FastAPI[FastAPI App] -->|SQL :5432| Postgres[(PostgreSQL)]
        end
    end

```
# Part 1 - Prerequisites

* Register at LinuxOne Community Cloud <a href="https://linuxone.cloud.marist.edu/#/register?flag=VM" target="_blank"  rel="noopener noreferrer">here</a> for a trial account and get an **Ubuntu VM**.

![register](images/ibm_linuxone_register.png)

# Part 2 – Setup your environment on LinuxONE VM
# Setup Guide

### Prerequisite:
- docker & docker compose
- jq
- make (optional)
```bash
sudo systemctl restart snapd
sudo apt update
sudo snap install docker
sudo snap install jq
```
```bash
sudo usermod -aG root linux1
# above changes won't apply until the user starts a new shell session
# Exit your current shell
exit
# Then log back in:
ssh -i mykey.pem linux1@<your-server>
```
```bash
git clone https://github.com/codesenju/api_pgmoviedb.git
cd api_pgmoviedb/
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
## Visit the api documentation: http://\<linux1-ip\>:8000/docs


# Part 3: Talk to your DB with AI.
## Prerequisites:
- [nodejs and npm installed](https://nodejs.org/en/download/)
```bash
npx -v
npm -v
node -v
```
![nodejs](./images/nodejs.png)
- [Visual Studio Code](https://code.visualstudio.com/download)

## Setup Copilot in VS Code
- Open Visual Studio Code
- Install and configure the GitHub Copilot extension
- Enable agent mode (check Copilot Labs or experimental settings)

    - 💡 Agent mode allows Copilot to interface with external tools (like databases) via MCP.

![add-mcp-server](./images/gifs/setup-copilot.gif)

## Configure the MCP Server

MCP stands for Model Context Protocol — it lets LLMs like Copilot securely communicate with things like databases.

Add this to your settings.json in VS Code:

- Ctrl + Shift + P (Cmd + Shift + P on MacOS), search for user settings.

```json
{
  "mcp": {
      "servers": {
          "postgres": {
              "command": "npx",
              "args": [
                  "-y",
                  "@modelcontextprotocol/server-postgres",
                  "postgresql://postgres:12345@148.100.77.189:5432/movie"
              ]
          }
      }
  }
}
```

![vs-code-settings](./images/gifs/add-mcp-server.gif)

### Ask Your First Question
Open Copilot Chat and ask Copilot something like:

`Study the postgres schema and tell me how many movies were released in 1992?`

Copilot will:

- Interpret your natural language prompt.
- Connect to the PostgreSQL database via MCP.
- Generate and run a SQL query.
- Return the result directly in the editor.

![ai-query](./images/gifs/query.gif)

---

# 📜 Disclaimer

This project uses IMDb data, which is freely available for personal and non-commercial use under IMDb’s [Terms and Conditions](https://www.imdb.com/conditions).

### ⚠️ Important Notes:

- The IMDb dataset used in this project is obtained from IMDb’s [official data dumps](https://datasets.imdbws.com/).
- **IMDb owns all rights to the data**. This project does **not** claim ownership of any IMDb data.
- The use of IMDb data in this project is solely for educational, research, or personal testing purposes.
- This project is **not affiliated with or endorsed by IMDb or IBM** in any way.
- Redistribution of IMDb data outside of IMDb’s licensed use is **prohibited**.

### Usage Terms

By using this project, you acknowledge that:

- You are responsible for complying with IMDb’s terms of service.
- You must not use this project or its dataset for commercial purposes.
- You must not publicly redistribute IMDb data unless explicitly permitted by IMDb.

