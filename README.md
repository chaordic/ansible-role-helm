Role Name
=========

Role to install any helm charts.

Requirements
------------

- Submodule depedencies

    - pyhelm


Example Playbook
----------------

- helm-charts.yml
```

  ---
- name: EKS
  hosts: 127.0.0.1
  connection: local
  gather_facts: True

  vars:
    env: prod

  vars_files:
    - "vars/k8s-cluster/eks-cloud-{{ env }}.yml"

  pre_tasks:
    - name: Load Helm Charts definitions from vars/k8s-cluster-main
      include_vars: "{{ item }}"
      with_items:
        - "{{ playbook_dir }}/vars/k8s-cluster/helm-charts/main.yml"
      register: temp_charts
      tags: always

    - debug:
       var: helm_charts

    - debug:
        msg: "{{ item }}"
      with_items: "{{ helm_charts }}"

  roles:
    - role: helm
      tags: helm
```

- vars/k8s-cluster/helm-charts/bootstrap.yml
```
helm_charts:
  - chart:
      name: aws-alb-ingress-controller
      version: 0.1.4
      source:
        type: repo
        location: http://storage.googleapis.com/kubernetes-charts-incubator
    state: present
    name: aws-alb-ingress-controller
    namespace: default
    values:
      awsRegion: us-east-1
      awsVpcID: vpc-1122333
      clusterName: dev
  - chart:
      name: docker-registry
      version: 1.6.3
      source:
        type: repo
        location: https://kubernetes-charts.storage.googleapis.com/
    state: present
    name: docker-registry
    namespace: default
```

# Attention
> Your Helm cli version is responsible to the creation of tiller version.