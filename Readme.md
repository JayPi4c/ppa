https://assafmo.github.io/2019/05/02/ppa-repo-hosted-on-github.html

or

https://github.com/marketplace/actions/github-pages-apt-repo


```bash
curl -s --compressed "https://jaypi4c.github.io/ppa/ubuntu/KEY.gpg" | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/jaypi4c.gpg >/dev/null
sudo curl -s --compressed -o /etc/apt/sources.list.d/jaypi4c.list "https://jaypi4c.github.io/ppa/ubuntu/jaypi4c.list"
sudo apt update
```