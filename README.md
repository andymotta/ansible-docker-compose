# ansible-docker-compose

The purpose of this project is to be able to test Ansible roles locally and in Docker containers, with docker-compose.

To provide an example, included are roles to create a complete local stack load-balanced by HAProxy and served by Nginx, [Compliments of Sysadmin Casts. ](https://sysadmincasts.com/episodes/47-zero-downtime-deployments-with-ansible-part-4-4)

The control node is Ubuntu and target "hosts" are Amazon Linux, with full init-systems and SSH access.  Basically, not the way you want to run containers but serves its purpose as a lightweight training environment.

### Getting Started
First install Docker for Mac: https://download.docker.com/mac/stable/Docker.dmg

```bash
chmod 0600 ./env/ansible*
docker-compose up -d
cat env/ssh_host_config >> ~/.ssh/config
ssh control # do this from the repo root
```

### Running the example playbooks:
```bash
ansible@control:~$ cd ansible/
ansible@control:~/ansible$ ansible-playbook site.yml
ansible@control:~/ansible$ ansible-playbook playbooks/stack_status.yml`
open http://localhost:8001 # on your Mac
```

After HAProxy role finishes, or when applying *ansible-playbook blog.yml*, continuously refresh <http://localhost:8001/haproxy?stats> to watch the backends being taken out of service 50% at a time to apply the blog.

`curl http://localhost:8001` a few times.  You will see "Served by web1/web2" cycling.

To see Apache benchmarks run `ansible-playbook playbooks/stack_status.yml`

### Vault
If you want to add any secrets, store them in ansible-vault:
`ansible-vault edit group_vars/all/vault`
* **Note**: This works because the vault password is injected during the Dockerfile-control build.  For testing only.

After you edit the vault, add the variable to `group_vars/all/vars.yml`
An example is included.

### How to re-build Docker images:
```bash
docker-compose down
docker-compose build
docker-compose up -d
```

#### Final Note:
Be sure not to copy the private key *env/ansible* anywhere.  It was generated for the purposes of this test stack, not to be uploaded anywhere else.

**ToDo**: Docker-compose is Version 1, could use an update
