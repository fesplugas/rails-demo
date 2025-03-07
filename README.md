# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...

## Deployment

Clone repository

```bash
git clone https://github.com/fesplugas/rails-demo.git ~/Code/fesplugas/rails-demo
```

Prepare Caddy configuration

```bash
cat << EOF | sudo tee /etc/caddy/Caddyfile
import /etc/caddy/vhosts/*
EOF

sudo mkdir -p /etc/caddy/vhosts/
sudo cp ~/Code/fesplugas/rails-demo/Caddyfile /etc/caddy/vhosts/test.francescesplugas.com
```

Restart Caddy

```bash
sudo systemctl caddy restart
```

Setup

```bash
cd ~/Code/fesplugas/rails-demo
bin/setup
```

Create environment variables

```bash
mise set RAILS_ENV=production
mise set SECRET_KEY_BASE=$(rails secret)
mise set MISE_ENV=production
```

Create a systemd service ...

```bash
cat << EOF | sudo tee /etc/systemd/system/rails-demo-puma.service
[Unit]
Description=Rails Puma Webserver
Wants=network-online.target
After=network.target network-online.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/Code/fesplugas/rails-demo
ExecStart=/bin/bash -lc '/home/ubuntu/.local/bin/mise exec -- rails server'

TimeoutSec=15
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

Enable and start it ...

```bash
sudo systemctl enable rails-demo-puma
sudo systemctl start rails-demo-puma
```

You can check the status with ...

```bash
sudo systemctl status rails-demo-puma
```

And website is accessible at [https://test.francescesplugas.com](https://test.francescesplugas.com).

If you need to apply changes ...

```
sudo systemctl stop rails-demo-puma
git pull
rails db:migrate
rails assets:precompile
sudo systemctl start rails-demo-puma
```
