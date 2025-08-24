# Troubleshoot and Create Ansible Playbook

### Task

Write a ansible playbook to create a file in a remote server App server 02

---

### Solution

Create the `playbook.yaml` file.

```yaml
---
- name: Touch /tmp/file.txt on remote server
  hosts: app
  become: true
  tasks:
    - name: Create empty file at /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
        owner: steve
        group: steve
        mode: '0755'
```

Create the `inventory` file as below.

```ini
[app]
stapp02
```

Execute the playbook with the following command:
`ansible-playbook -i inventory playbook.yaml`
