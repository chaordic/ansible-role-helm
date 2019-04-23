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
---
- name: EKS
  hosts: 127.0.0.1
  connection: local
  gather_facts: True

  vars:
    env: prod

  vars_files:
    - "vars/k8s-cluster/eks-cloud-{{ env }}.yml"

  vars_prompt:
    - name: "chart_var"
      prompt: "What Chart FILE do you want to load [vars/k8s-cluster/helm-charts/<FILE>.yml or * to ALL]?"
      private: no
      when: chart_var is not defined

  pre_tasks:
    - block:
      - name: Load Helm Charts definitions from vars/k8s-cluster/helm-charts
        include_vars: "{{ item }}"
        with_items:
          - "{{ playbook_dir }}/vars/k8s-cluster/helm-charts/{{ chart_var }}.yml"
        register: temp_charts1
        tags: always

      when: chart_var != "*"

    - block:
      - name: Load all Helm Charts definitions from vars/k8s-cluster/helm-charts
        include_vars: "{{ item }}"
        with_fileglob:
          - "{{ playbook_dir }}/vars/k8s-cluster/helm-charts/*.yml"
        register: temp_charts2

      when: chart_var == "*"

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