# Setup puppet lab environment
Setup puppet lab environment:

- 1 master, 1 or more client nodes.
- Puppet version 5
- Master using Centos 7

On master server install package `puppetserver`.

```
sudo rpm -Uvh https://yum.puppet.com/puppet5-release-el-7.noarch.rpm
sudo yum install puppetserver
```

Then update file `/etc/sysconfig/puppetserver`

Update path for `puppetserver`

> echo "Defaults  secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/puppetlabs/bin" >  /etc/sudoers.d/99_extra

Change the value below to suitable value
`JAVA_ARGS="-Xms2g -Xmx2g -XX:MaxPermSize=256m"`
Then start the service `puppetmaster`. It can take few minutes to finish.
```
systemctl start puppetmaster
systemctl status puppetmaster
systemctl enable puppetmaster
```
On client centos server install the `puppet` agent
```
sudo rpm -Uvh https://yum.puppet.com/puppet5-release-el-7.noarch.rpm
sudo yum install puppet
systemctl start puppet
systemctl status puppet
systemctl enable puppet
```

Update path for `puppetserver`
> echo "Defaults  secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/puppetlabs/bin" >  /etc/sudoers.d/99_extra

Register client node
> sudo puppet agent --fingerprint

On master 
> sudo puppetserver ca sign --certname node1.ec2.internal

We can use the command to list/approve all `ca` 
```
puppetserver ca list
puppetserver ca sign
```

If your client server is using `ubuntu 18` you just need to update the package repo then do the same step above
```
wget https://apt.puppetlabs.com/puppet5-release-bionic.deb
sudo dpkg -i puppet5-release-bionic.deb
sudo apt update
sudo apt install puppet-agent
sudo systemctl start puppet
sudo systemctl enable puppet
```

Test cookbook on client node by running the command

> puppet agent -t
