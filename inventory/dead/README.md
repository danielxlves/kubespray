# Provisionamento do Cluster Kubernetes com Kubespray

Esta documentação descreve os passos para configurar seu cluster Kubernetes utilizando o Kubespray, habilitando o Ingress NGINX e o Cert Manager.



## 1️⃣ Clonar o Repositório do Kubespray

Primeiramente, clone o repositório oficial do Kubespray:

```bash
git clone https://github.com/kubernetes-sigs/kubespray.git /var/k8s/kubespray
```



## 2️⃣ Clonar o Repositório do Inventário do Cluster

Dentro do diretório do Kubespray, clone o repositório que contém o inventário do cluster para a pasta `inventory`:

```bash
git clone https://codelab.ifrn.edu.br/dead-zl/infra/cluster_k8s-init /var/k8s/kubespray/inventory
```



## 3️⃣ Habilitar o Ingress NGINX

Edite o arquivo `group_vars/k8s_cluster/k8s-cluster.yml` e defina a seguinte flag para ativar o Ingress NGINX:

```yaml
ingress_nginx_enabled: true
```



## 4️⃣ Configurar Parâmetros Adicionais no `addons.yml`

No arquivo `group_vars/k8s_cluster/addons.yml`, adicione as configurações abaixo para ajustar os parâmetros do Ingress NGINX e habilitar o Cert Manager:

```yaml
ingress_nginx_host_network: true
ingress_nginx_nodeselector:
  "ingress-ready": "true"
ingress_nginx_tolerations:
  - key: "ingress-ready"
    operator: "Exists"
    effect: "NoSchedule"
ingress_publish_status_address: ""

cert_manager_enabled: true
cert_manager_tolerations:
  - key: "certmanager-ready"
    operator: "Exists"
    effect: "NoSchedule"
```



## 5️⃣ Iniciar o Cluster

Acesse o diretório `/var/k8s/kubespray`, ative o ambiente virtual e inicie a criação do cluster utilizando o Ansible:

```bash
source venv/bin/activate
ansible-playbook -i inventory/dead/inventory.ini -b cluster.yml
```



## 6️⃣ Rotular o Nó do Control Plane

Após a criação do cluster, rotule o nó desejado (geralmente o nó do Control Plane) para que ele aceite o Ingress Controller e o Cert Manager. Execute os comandos abaixo, substituindo `"Nó com ip externo"` pelo nome ou IP do nó:

```bash
kubectl label node "Nó com ip externo" ingress-ready=true
kubectl label node "Nó com ip externo" certmanager-ready=true
```

