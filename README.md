Following guide is written for OS Fedora 34 to build AWS AMI using Hashicorp Packer according to the following criteria: 
- base ami: the latest Amazon Linux (x86_64);
- authorization ssh key included;
- _htop_ and _iotop_ packages pre-installed;
- tags _[test: test]_ added; 
- packer outputs resulting AMI ID to the file _manifest.json_;
- 
__Prerequisites__
- AWS account with created key-pair named _packer_key_pair_;
- Cloud instance/VM with Fedora 34 OS running;
- private key named _packer_key_pair.pem_ located at your Fedora Instance/VM;

In order to successfully build the image you need to follow the next steps: 
- Install Packer: 
```
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --add-repo https://rpm.releases.hashicorp.com/fedora/hashicorp.repo
sudo dnf -y install packer
```
- Install jq to read resulting AMI ID and git to download packer config:
```
sudo yum -y install jq
sudo yum -y install git
```
- Clone repo and proceed to cloned directory:
```
git clone https://github.com/sergeyscherba/DOW_test_task.git
cd DOW_test_task
```
- Set environment variables to contain your AWS access key,secret key and default region(or add following lines to /home/fedora/.bash_profile):
```
export AWS_ACCESS_KEY_ID=<your_access_key>
export AWS_SECRET_ACCESS_KEY=<your_secret_key
export AWS_DEFAULT_REGION=<your_default_region>
```
- Copy ssh private key file packer_key_pair to /home/fedora/.ssh/
```
cp /home/fedora/packer_key_pair.pem /home/fedora/.ssh/packer_key_pair.pem
```
- Build the image
 ``` 
 packer build packer-config.json
 ```
- When image is builded you can read new AMI ID using following commands:
```
AMI_ID=$(jq -r '.builds[-1].artifact_id' manifest.json | cut -d ":" -f2)
echo $AMI_ID
```
