###### ansible-playbook 
`ansible-playbook -b -i inventory/inventory.cfg cluster.yml`

@import "inventory/inventory.cfg" {as=yaml}
@import "test.yml"

---
###### role: kubespray-defaults
`roles/kubespray-defaults/defaults/main.yaml`
@import "roles/kubespray-defaults/defaults/main.yaml"
`roles/kubespray-defaults/meta/main.yml`
@import "roles/kubespray-defaults/meta/main.yml"
`roles/kubespray-defaults/tasks/main.yaml`
@import "roles/kubespray-defaults/tasks/main.yaml"

###### role: bastion-ssh-config
`roles/bastion-ssh-config/tasks/main.yml`
@import "roles/bastion-ssh-config/tasks/main.yml"
`roles/bastion-ssh-config/templates/ssh-bastion.conf`
@import "roles/bastion-ssh-config/templates/ssh-bastion.conf"

###### role: bootstrap-os
@import "roles/bootstrap-os/defaults/main.yml"
@import "roles/bootstrap-os/files/bootstrap.sh"
`@import "roles/bootstrap-os/files/get-pip.py"`
@import "roles/bootstrap-os/files/runner"
@import "roles/bootstrap-os/defaults/main.yml"


###### role: kubernetes/preinstall
@import "roles/kubernetes/preinstall/defaults/main.yml"

@import "roles/kubernetes/preinstall/tasks/main.yml"


###### role: download
`roles/download/defaults/main.yml`
@import "roles/download/defaults/main.yml"
`roles/download/defaults/main.yml`
@import "roles/download/tasks/main.yml"
`roles/download/tasks/download_container.yml`
@import "roles/download/tasks/download_container.yml"
@import "roles/download/tasks/download_file.yml"
`roles/download/tasks/download_prep.yml`
@import "roles/download/tasks/download_prep.yml"
`roles/download/tasks/set_docker_image_facts.yml`
@import "roles/download/tasks/set_docker_image_facts.yml"
`roles/download/tasks/sync_container.yml`
@import "roles/download/tasks/sync_container.yml"

@import "roles/download/meta/main.yml"


---
###### prepare
所有节点的网路之间可以互相通信。
部署节点(这边为 master1)对其他节点不需要 SSH 密码即可登入。
所有节点都拥有 Sudoer 权限，并且不需要输入密码。
所有节点需要安装 Python。
所有节点需要设定/etc/hosts解析到所有主机。
修改所有节点的/etc/resolv.conf

`ansible-playbook -i inventory/hosts_tests.yml prepare_pbk.yml`

@import "inventory/hosts_tests.yml"
@import "./prepare_pbk.yml"