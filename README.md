# Docker DOMJudge Setup

## DOMServer
- Create new Digital Ocean one-click Docker host (recommend 1 CPU, large storage) called `domjudge-domserver`
- (Optional) Assign `domjudge-domserver` a floating IP
- SSH into `domjudge-domserver`
- `git clone https://github.com/gozzarda/domjudge_docker_compose.git`
- `cd domjudge_docker_compose/domserver`
- Edit `domserver.env` and **change the root and user passwords**
- `docker-compose up --detach`
- Navigate to `domjudge-domserver` in a web browser, and log into DOMJudge with admin/admin
  - **Change the admin password**
  - **Change the judgehost password**
- (Optional) create a Digital Ocean snapshot so you can restore later

## Judgehost(s)
- Create new Digital Ocean one-click Docker host (recommend 1 CPU, small storage) called `domjudge-judgehost-0`
- SSH into `domjudge-judgehost-0`
- `git clone https://github.com/gozzarda/domjudge_docker_compose.git`
- `cd domjudge_docker_compose/judgehost`
- Edit `judgehost.env` and **change the judgehost password and domserver url**
- `docker-compose up --detach`
- (Optional) create a Digital Ocean snapshot so you can restore later
