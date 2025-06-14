# Ansible Guided Tutorial

This repository accompanies a video tutorial on how to create a simple Ansible playbook. It is written for absolute beginners with no programming experience.

## Requirements
- **Ansible**: make sure Ansible is installed on your system. You can verify with:
  ```bash
  ansible --version
  ```

## 1. Create an inventory file
An inventory tells Ansible which machines to manage. For this tutorial we use the local machine only.

File: `inventory/hosts`

```
localhost ansible_connection=local
```

## 2. Write your first playbook
A playbook is a YAML file. Spacing and hyphens matter!

File: `playbooks/basic-playbook.yml`

```
---
- name: Basic example playbook
  hosts: localhost
  gather_facts: false

  vars:
    message: "Hello from Ansible!"

  tasks:
    - name: Create a sample file
      copy:
        content: "{{ message }}"
        dest: "hello.txt"
```

### Key points
- `---` indicates the start of a YAML document.
- `- name: ...` starts a list item. Note the space after the hyphen.
- `hosts:` specifies where the tasks will run.
- `vars:` defines variables.
- Curly braces `{{ }}` allow you to reference variables.
- YAML uses indentation (two spaces recommended) to show structure.

## 3. Run the playbook
Execute the playbook with:

```bash
ansible-playbook -i inventory/hosts playbooks/basic-playbook.yml
```

After running, check that `hello.txt` was created with the text `Hello from Ansible!`.

Feel free to experiment by changing the `message` variable in the playbook and running it again.

## 4. Next steps
- Explore more modules in the [Ansible documentation](https://docs.ansible.com/).
- Try adding additional tasks below the `Create a sample file` task in the playbook.

Enjoy learning Ansible!

## 5. Launch an AWS EC2 instance
The `amazon.aws.ec2_instance` module can create virtual machines in your AWS account. You must have AWS credentials configured (environment variables or `~/.aws/credentials`) and the [`amazon.aws`](https://docs.ansible.com/ansible/latest/collections/amazon/aws/) collection installed.

File: `playbooks/aws-ec2.yml`

```yaml
---
- name: Launch an EC2 instance
  hosts: localhost
  gather_facts: false

  vars:
    instance_name: myExample
    region: us-east-1
    key_name: mykey
    image_id: ami-1234567890abcdef0

  tasks:
    - name: Launch instance
      amazon.aws.ec2_instance:
        name: "{{ instance_name }}"
        key_name: "{{ key_name }}"
        instance_type: t2.micro
        image_id: "{{ image_id }}"
        region: "{{ region }}"
        wait: true
      register: ec2

    - name: Wait for SSH to become available
      ansible.builtin.wait_for:
        host: "{{ ec2.instances[0].public_ip_address }}"
        port: 22
        timeout: 300
```

Run it with:

```bash
ansible-playbook playbooks/aws-ec2.yml
```

## 6. Advanced Ansible concepts
- **Conditionals**: run tasks only when conditions are met using `when:`.
- **Loops**: create multiple resources with `loop:` or `with_items:`.
- **Handlers**: trigger actions (like service restarts) only when tasks report changes.
- **Roles**: organize files and variables for larger projects.

These ideas build on the basics and help automate more complex environments.

