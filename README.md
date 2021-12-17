
- Ansible 2.9+

## Local Ansible Development
It's much quicker to run ansible against a local VM during development/testing instead of running through a pipeline. To setup a local environment follow these steps:
1. Create a [Windows Server 2019](https://www.microsoft.com/en-US/evalcenter/evaluate-windows-server-2019?filetype=ISO) virtual machine.
3. Create a `./ansible/inventory` file with the following contents (Changing the username and password to your Windows VM Administrator credentials)
```ansible
[win2019_standard_web]
127.0.0.1 ansible_connection=winrm ansible_user=Administrator ansible_password=Password1! ansible_winrm_server_cert_validation=ignore
```
4. You can now run ansible against your VM using this command:
```sh
ansible-playbook -i ansible/inventory ansible/playbook.yml
```
5. You can change the hostname in the `inventory` file to target installs for different AMIs. The format follows this standard: `{operatingsystem}_{amirole}` so for example, the `win2019_standard_web` runs the configurations for the Windows Web Servers, whereas `macos_acq` will configure a MacOS operating system with the ACQ services.
