Simple Node.js app for EC2 practice

Files:
- index.js  : simple HTTP server (no dependencies)
- package.json
- start.sh  : helper start script

Quick deploy steps (Amazon Linux 2):

1) Create an EC2 instance (Amazon Linux 2) and open port 3000 in its security group.

2) SSH to instance and install Node.js:

sudo yum update -y
curl -sL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs

3) Copy project to EC2 (from your local machine):

scp -r simple-node-ec2 ec2-user@<EC2_IP>:/home/ec2-user/

4) On EC2:

ssh ec2-user@<EC2_IP>
cd ~/simple-node-ec2
chmod +x start.sh
# Run directly
PORT=3000 node index.js
# or use the helper
./start.sh

5) Visit http://<EC2_IP>:3000 in your browser (ensure security group allows inbound TCP 3000).

Optional: run with pm2 or create a systemd service to keep it running on reboot.

Example systemd unit (save as /etc/systemd/system/simple-node.service):

[Unit]
Description=Simple Node App
After=network.target

[Service]
Type=simple
User=ec2-user
WorkingDirectory=/home/ec2-user/simple-node-ec2
Environment=PORT=3000
ExecStart=/usr/bin/node index.js
Restart=on-failure

[Install]
WantedBy=multi-user.target

Then enable and start:
sudo systemctl daemon-reload
sudo systemctl enable --now simple-node.service
