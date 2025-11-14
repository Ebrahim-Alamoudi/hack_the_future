This repository contains the work I completed during the Hack The Future 2025 – Aquatopia Hackathon, where I participated in the “Dive into Deep Waters of Linux” challenge.

The task:
Build, configure, secure, containerize, and automate the deployment of a Linux-based application (PirosPC) behind an NGINX reverse proxy with TLS 1.3, fully automated using Ansible, running under rootless Podman, and documented in a final pitch.

I completed the entire challenge alone, while most participants worked in groups.

📌 What I Built
✔️ Fully working NGINX reverse proxy

Redirects all HTTP → HTTPS

Strictly served under the canonical hostname www9.htf25.qubr.be

Configured with TLS 1.3, HTTP/2, and hardened security headers.

✔️ Podman container running the PirosPC app

Pulled from my public Quay image:
quay.io/ebrahim_alamoudi/hack_the_future:latest

Runs under rootless Podman

Managed by systemd using a generated .service file.

✔️ Ansible automation

A full automation pipeline that:

Installs NGINX + dependencies

Configures TLS 1.3

Deploys the reverse-proxy vhost

Configures firewalld rules

Enables SELinux booleans

Starts/Enables Podman systemd service

Deploys everything repeatably and cleanly

✔️ SELinux & Firewall

Set required SELinux rules (container networking + web access)

Ensured firewalld allowed ports 80 and 443

Verified with curl + journalctl

✔️ Final Pitch

Delivered a final pitch explaining the full setup, automation, mistakes made, and lessons learned.

🧠 What Went Wrong (And How I Solved It)

This hackathon wasn’t smooth. Here’s what I struggled with and how I fixed each issue:

❌ 1. Wrong hostname (web9) in configs

Originally, everything referenced web9.htf25.qubr.be.
But the required host was www9.

✔️ Fixed all configs, NGINX vhosts, and Ansible templates to use the correct hostname.

❌ 2. TLS 1.3 not being applied

Early versions of the NGINX config weren’t forcing TLS 1.3 only.

✔️ Updated ssl_protocols TLSv1.3; and validated via curl + ssldebug.

❌ 3. Podman container was empty

At first, the container had no app files, so nothing worked.

✔️ Rebuilt the container locally
✔️ Pushed a correct image to Quay.io
✔️ Pulled the fixed build on the VM and validated it runs

❌ 4. Difficulties pushing to Quay.io

Wrong robot account usernames, tokens, and login names caused repeated failures.

✔️ Generated correct robot account
✔️ Logged in with:
podman login quay.io -u ebrahim_alamoudi+bot -p <TOKEN>
✔️ Successfully pushed the image

❌ 5. Ansible SSH key issues

GitHub and VM SSH keys were misconfigured.

✔️ Generated fresh ed25519 keys
✔️ Added correct public key to GitHub
✔️ Re-connected the controller to the target VM

❌ 6. SELinux blocking the reverse proxy

Initial traffic didn’t pass.

✔️ Enabled required boolean:

setsebool -P httpd_can_network_connect 1

❌ 7. Old vhost leftovers interfering

Default NGINX vhost caused name collisions.

✔️ Removed default vhost via Ansible tasks
✔️ Ensured only www9.conf exists

🧪 Validation Steps I Performed

Everything was tested using:

curl -I http://www9.htf25.qubr.be
curl -kI https://www9.htf25.qubr.be
podman ps
sudo systemctl status container-pirospc
hostname -I
ss -tulpn | grep nginx


Everything returned the expected results after fixes.

📁 Repository Structure
/ansible
  ├── playbooks/
  ├── inventory/
  └── roles/
       └── nginx/
           ├── tasks/
           ├── templates/
           └── files/
docker/
nginx/
docs/
README.md   ← (this file)

🎯 Final Result

Fully working TLS 1.3 secured deployment

Complete Ansible automation

Hardened NGINX proxy

Publicly available container image

Live DNS-mapped site under www9.htf25.qubr.be

Top 10 performance (9th place) with a 78% score

Delivered a working final pitch

🙏 Acknowledgements

Special thanks to the Hack The Future organization for a great experience and challenge setup.
Even though I worked solo while others were in teams, I learned a lot and pushed myself through every failure and fix.
