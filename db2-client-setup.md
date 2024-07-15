## Install AWS CLI v2 on Cloud9

# run these commands after the DBendpoint is known and the SecretManager steps
# Update the AWS CLI v2
which aws
ls -l /usr/local/bin/aws
sudo rm /usr/local/bin/aws
sudo rm /usr/local/bin/aws_completer
sudo rm -rf /usr/local/aws-cli
sudo rm -rf /usr/local/aws
sudo rm /usr/bin/aws
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip -q awscliv2.zip
sudo ./aws/install
/usr/local/bin/aws --version
rm awscliv2.zip


# Resize Cloud9 Volume to 50G
source <(curl -s https://raw.githubusercontent.com/aws-samples/aws-swb-cloud9-init/mainline/cloud9-resize.sh)




## Install IBM Db2 Run Time Client on Cloud9

# Download and Install the IBM Db2 Run Time Client.
cd
mkdir db2client
cd db2client
wget  https://ws-assets-prod-iad-r-cmh-8d6e9c21a4dec77d.s3.us-east-2.amazonaws.com/741f2fa6-13d6-41d5-87e2-6672e1fe6789/v11.5.9_linuxx64_rtcl.tar.gz
sudo  tar -xzf v11.5.9_linuxx64_rtcl.tar.gz
cd rtcl
# Source Db2 installation path variables.Typical client install location: /opt/ibm/db2/V11.5
echo "export IBM_DB_HOME=/opt/ibm/db2/V11.5" >> ~/.bashrc
echo 'export LD_LIBRARY_PATH="${IBM_DB_HOME}/lib":$LD_LIBRARY_PATH' >> ~/.bashrc
echo 'export PATH="${IBM_DB_HOME}/bin":"${IBM_DB_HOME}/instance":$PATH' >> ~/.bashrc
echo 'export DB2INSTANCE=ec2-user' >> ~/.bashrc
source ~/.bashrc
sudo ./db2_install -f sysreq -y -b $IBM_DB_HOME
rm ~/db2client/v11.5.9_linuxx64_rtcl.tar.gz


## Creating and configuring a Db2 client instance

# Create Db2 Client instance
cd /opt/ibm/db2/V11.5/instance
sudo ./db2icrt -s client ec2-user
db2level


## Catalog Node and Database on Db2 Client Instance

# Catalog Node and RDS Db2 database. use the DBendpoint from step 1
db2 "catalog tcpip node db2node remote <DBEndpoint> server 50000"
db2 "catalog database <Database name> at node db2node authentication server_encrypt"
db2 list node directory
db2 list database directory

# Catalog Node and RDS Db2 database. use the DBendpoint from step 1
db2 "catalog tcpip node db2node remote rds-db2-target.cfga2u00o7kk.us-east-2.rds.amazonaws.com server 50000"
db2 "catalog database rdsadmin at node db2node authentication server_encrypt"
db2 list node directory
db2 list database directory


## Connect to RDS Db2 database
db2 "connect to <Database Name> user masteruser using <password>"

db2 "connect to rdsadmin user masteruser using <password>"
         
