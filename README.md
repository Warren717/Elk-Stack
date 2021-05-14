## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Elk Diagram](./Diagrams/Finished_ELK_Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.

  ![Install_Elk](./Ansible/Install-Elk.yml)
  - filebeat-playbook.yml
  - metricbeat-playbook.yml

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to      restricting in-bound access to the network.

What aspect of security do load balancers protect?

- Load Balancers defend an organization again distributed denial-of-serivce (DDoS) attacks. It does this by shifting attack traffic from the corporate server to a public cloud provider.

What is the advantage of a jump box?

 - A Jump Box is a system on a network used to access and manage devices in a seperate security zone. A Jump Box is a hardened and monitored device that spans two dissimilar security zones and provides a controlled means of access between them.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the jump box and system network.

What does Filebeat watch for?

- Filebeat is a logging agent that monitors changes to the machine and and logs. Filebeat can also ship the recorded logs to an output of choice for further processing.

What does Metricbeat record?

- Metricbeat collects the metrics and statistics of the operating system and services that are running on the server. The collected results can also be shipped to an output of choice.

The configuration details of each machine may be found below.


| Name      | Function  | IP Address | Operating System |
|-----------|-----------|------------|------------------|
| Jump- Box | Gateway   | 10.0.0.1   | Linux            |
| Web-1     | Webserver | 10.0.0.7   | Linux            |
| Web-2     | Webserver | 10.0.0.6   | Linux            |
| ELK-Server| Monitoring| 10.1.0.4   | Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

- Add whitelisted IP addresses

  - 5061 Kibana Port

Machines within the network can only be accessed by the jump box provisioner.

Which machine did you allow to access your ELK VM? What was its IP address?

- The IP address of the machine that I allowed to access the ELK VM was: Workstation IP

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP Addresses |
|---------------|---------------------|----------------------|
| Jump Box      | Yes                 | Workstation IP       |
| Web-1         | No                  | 10.1.0.4             |
| Web-2         | No                  | 10.1.0.4             |
| Load Balancer | Yes                 | Workstation IP       |
| Elk Server    | Yes                 | Workstation IP       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because services running can be limited, system installation and update can be streamlined, and processes become more replicable.

What is the main advantage of automating configuration with Ansible?

- Flexible: You can orchestrate the entire application enviroment no matter where it is deployed. You can also customize it based upon your needs.

- Agentless: You do not need to install any other software of firewall ports on the client systems you want to automate.


The playbook implements the following tasks: 

- Install Docker, install Python, Install Docker Python:


~~~
# name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present
~~~
  - This will increase the Virtual Memory for Elk server machine as well as system control:
~~~
  # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
~~~
- Downloads and launches a Docker Elk container:
~~~
# Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
 ~~~

### Target Machines & Beats
This ELK server is configured to monitor the following machines:

- Web-1: 10.0.0.7
- Web-2: 10.0.0.6

We have installed the following Beats on these machines:

- Filebeat
- Metric Beat

These Beats allow us to collect the following information from each machine:

- Filebeat: Monitors the log directories or specific log files and forwards them either to Elasticsearch or Logstash for indexing.

- Metric Beat: Collects statistics and metrics of the system. We can monitor system health, CPU, and memory usage of the system we are working on.


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

- Copy the playbook file to Ansible Control Node
- Update the hosts file to include webserver and elk
- Update the /etc/hosts file to include the IP addresses of the Elk and webservers
- Run the playbook, and navigate to http://Elk_VM_Public_IP:5601/app/kibana to check that the installation worked as expected


Which file is the playbook? Where do you copy it?

- Playbook file: "filebeat-playbook.yml"

- Copy ".yml" file inside of the home directory in a custom folder

Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?

- Update the "filebeat-configuration.yml", specify which machine to install by updating the IP addresses of the Web and Elk servers inside the "Hosts" file and select which group to run inside of Ansible.




Which URL do you navigate to in order to check that the ELK server is running?

- http://your.ELK-VM.external.IP:5601/app/kibana


