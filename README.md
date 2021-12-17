# Editorial Manager AMI Bakery
This repository contains the Packer and Ansible files used to create the base images for the editorial manager product. Packer is used to create AMIs within AWS and it calls upon Ansible to perform the configuration of each AMI role.

The following roles are used to configure roles for editorial manager:

| Packer Role | Operating System | Description |
| ----------- | ----------- | ---------- |
| web | Windows 2019 Standard | Configures the EM web service |
| api | Windows 2019 Standard | Configures the EM API service |
| batch | Windows 2019 Standard | Configures the EM batch processing service |
| asynch | Windows 2019 Standard | Configures the EM asynch processing service |
| izenda | Windows 2019 Standard | Configures the EM reporting izenda service |
| ad | Windows 2019 Standard | Configures the Aries AWS active directory server |
| database | Windows 2019 Standard | Configures SQL Server 2019 Standard Edition |
| acq | MacOS | Configures the ACQ processing server |
| typefi | Windows 2019 Standard | Configures the Typefi service |
| indesign | Windows 2019 Standard | Configures the Adobe InDesign service |
| solr | Amazon Linux 2 | Configures the Solr search service (*Subject to confirmation of project scope) |

## Dependencies
- Packer 1.7+
- Ansible 2.9+

## Local Packer Development
You can run the packer scripts locally for testing. You will need to ensure that your machine has Packer and the AWS SDK installed, and is configured with AWS credentials for the tools account.

In the root directory run the following command, passing in the required variables:
```sh
packer build \                        
  -var 'vpc_id={vpcid_from_aws}' \
  -var 'subnet_id={subnetid_from_aws}' \
  -var 'region=us-east-1' \
  -var 'dry_run=true' \
  -var 'role=web' \
  packer/windows.pkr.hcl
```
You may need to set your `AWS_PROFILE` environment variable to the name of your AWS Credentials Profile, depending on how your environment is configured.

## Local Ansible Development
It's much quicker to run ansible against a local VM during development/testing instead of running through a pipeline. To setup a local environment follow these steps:
1. Create a [Windows Server 2019](https://www.microsoft.com/en-US/evalcenter/evaluate-windows-server-2019?filetype=ISO) virtual machine.
2. Run the powershell script found in `./packer/scripts/setup-winrm.ps1` on your VM so that Ansible can connect to it.
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