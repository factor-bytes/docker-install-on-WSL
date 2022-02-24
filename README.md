# docker-install-on-WSL
Install docker on WSL(Ubuntu) without docker desktop





Install docker compose
1. Run below command to install latest version of dockere compose
```
$  sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. Give executable permissions
```
$ sudo chmod +x /usr/local/bin/docker-compose
```

3. Test Installation
```
$ docker-compose --version
```

