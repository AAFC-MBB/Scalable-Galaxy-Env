# Scalable Galaxy Environment

This is a much simpler version of the existing CloudMan software for creating Galaxy clusters. This software system works by staging a web server and a database server in Openstack using some existing parameters outlined below. Once those servers are set-up, the web server sets up an initial handler server. A cron job then monitors the Galaxy database to decide whether or not to scale the existing Galaxy handlers, based on the number of jobs that exist on each handler.

The heat template, currently called webAndDBTemplate.yaml needs to be placed on a controller node for your environment. Currently untested with the Horizon dashboard.

Requirements:
* It isn't necessary that your instances have DNS resolution but it makes some things easier, such as reducing the time needed to log into your Openstack instances.

### Private Key Pair
1. Public-private key pair available within Openstack. The private key should be available on the controller from which you put the heat template on. The correct name of the public and private key names should be updated in the heat template.
* Line 10: (Key name in openstack)
* Line 11: (Private key file name)
* Line 68: (Private key file name, Private key file name)
* Line 75: (Private key file name after /home/ubuntu/), (File Path for private key on controller)

### Networking
2. You need a private and public network accessible from Openstack. Obtain the net-ids for both and substitute in the parmeters section of the heat template. To get the net-ids, on the controller node type `neutron net-list` and `neutron subnet-list`.
* Line 35: (Private network id)
* Line 39: (Private subnet id)
* Line 43: (Public network id)

### Image
3. You will need an Ubuntu image, preferably 14.04. Here is a link to a website where you can get the image for free: http://releases.ubuntu.com/14.04/. The name of the image needs to be changed in the heat template. To get the image id from the Openstack API type: `nova image-list`
* Line 20: (Image id)

### Authentication with Openstack
4. You will need to change the relevant information for your Openstack account. Since ansible will take care of creating and deleting instances, it needs Openstack credentials to do this, and therefore an rc file. It is easiest to just provide the relevant information in the file rather than transfer a copy of your openrc file to the web server. Here are the variables that will need to be changed in the web server user_data parameter: OS_USERNAME, OS_PASSWORD, OS_TENANT_NAME, OS_AUTH_URL, OS_REGION_NAME, ENDPOINT_TYPE, OS_INTERFACE, OS_IDENTITY_API_VERSION. Again these parameter values can be found from your admin-openrc file on the Openstack Horizon dashboard. It is under Access & Security -> API Access -> Download Openstack RC File. Lines to be changed:
* Line 129: (OS_USERNAME)
* Line 130: (OS_PASSWORD)
* Line 131: (OS_TENANT_NAME)
* Line 132: (OS_AUTH_URL)
* Line 134: (ENDPOINT_TYPE)
* Line 135: (OS_INTERFACE)
* Line 136: (OS_IDENTITY_API_VERSION)

### Galaxy Version
5. Make sure the git link for Galaxy is still accessible. The one in the file is currently: https://github.com/galaxyproject/galaxy.git, using release 17.01.

### Security Group
6. If running this template for the first time, the template can be used as is and it will create the appropriate security group for you. If running it for a second time, and any time after that, you will need to comment out the resource that creates the security group called 'external access' which encompass lines 153- 180.

# Instructions:
1. Once the heat template is on a controller node, type in this command: heat stack-create -f webAndDBTemplate.yaml "stack-name". To follow the logs, it will be on the instances created, in /var/log/cloud-init-output.log.
2. Once set up, the Galaxy cluster will be accessible using the ip address of the web server on port 8080.
