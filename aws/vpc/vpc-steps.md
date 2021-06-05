VPC = virtual public cloud

You can have multiple vpcs in a region (max 5 per region - soft limit)

Max CIDR per vpc is 5
Min size is /28 : 16 addresses
Max size is /16 : 65536

Because VPC is private, only private ip ranges are allowed

Your vpc cidr range should not overlap with your other network (ex : corporate)



# Create VPC
- Name of the vpc
- ipv4 cidr bloc : 10.0.0.0/16
- Ipv6 cidr bloc : no ipv6 cidr bloc
- tenancy : default

Tenancy : 

Vous pouvez exécuter des instances dans votre VPC, sur du matériel dédié à locataire unique. 

Sélectionnez Dédié pour être certain que les instances lancées dans ce VPC sont des instances Dedicated Tenancy, quelle que soit la valeur de l’attribut de location spécifiée au lancement.

 Sélectionnez Par défaut pour être certain que les instances lancées dans ce VPC utilisent la valeur de l’attribut de location spécifiée au lancement.




 Lorsque le vpc est créé, une table de routage principale ainsi qu'un network ACL principal sont attribués


 Once the vpc created, we can cedit the CIDRS to add or remove cidrs.


# Create the subnets

Subnets are tied to a specific availability zone 

A good practice is to have public subnet with smaller size because it will contain only the load balancer

and in the private subnet we will put all the applications 

- Name of the subnet
- availability zone
- ipv4 cidr block


Aws reserves 5 ip addresses (first 4 and last one
in each subnet )
These ip addresses are not available and cannot be assigned to an instance


For instance , for 10.0.0./24

10.0.0.0 : Network address
10.0.0.1: Reserved by AWS for vpc router
10.0.0.2: reserved by AWS for mapping to Amazon proivded DNS
10.0.0.3 : reserved by AWS for future use
10.0.0.255 : Network broadcast address


# Enable auto-assign ip settings for the public subnets

Par défaut, les sous-réseaux personnalisés ont l'attribut d'adressage public d’IPv4 défini sur faux et pour les sous-réseaux par défaut sur vrai.

 Une exception est un sous-réseau personnalisé créé par l'assistant de lancement d'instance Amazon EC2. Celui-ci définit l'attribut sur vrai.


 # Create an Internet Gateway :

 - Internet Gateway  helps our vpc instances connect with the Internet 
 - It scaled horizontally and is HA redundant
 - Must be created separately from vpc
- **One vpc can only be attached to one IGW and viceversa**
- Internet Gateway is also a NAT for instances who have public IPV4 address
- Internet Gateway on their own do not allow internet access
- Route tables must be edited   

1- Créer l'internet gateway 
2- Attacher l'IGW au VPC



# Create the Route table for the  subnet and make sure that it points to the IGW

Rules for the main route table :

10.0.0.0/16 ==> local

We are going to create another route table 

and add the following route :

0.0.0.0/0  ===> IGW of our vpc

A route table is created first under a vpc
then we must change the routes and associate it 
to one or many subnets


# Create a NAT Gateway for the private subnets 

AWS managed NAT, higher bandwitdth , better availability , no admin
Pay by the hour for usage and the bandwidth 

**NAT is created in a specific AZ, uses an EIP**

Cannot be used from the instances in the same subnet

It must be created in a public subnet 

It requires an IGW

Private subnet ==> Nat Gatewa ==> IGW


5 Gbps of bandwidth with automatic scaling up to 45Gbps

No security groups to manage 

Nat gateway is resilient within a single AZ

**So we must create a NAT Gateway in each public subnet  for fault tolerance**

So we must have NAT Gateway in public sunbnet of AZ A
and a NAT gateway in public subnet of AZ B 



## Steps :

Choose a name
Choose the subnet for the nat gateway
Allocate an EIP
Edit the route table for the private subnet to include the nat gateway 


**NAT gateway may need some time to get created**

## Comparison of NAT Gateway and NAT instances

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html


# DNS resolution options and route 53 private zones

- enableDnsSupport : Default is True

Helps decide if DNS resolution is supported for the VPC

If true, queries the aws dns server at 169.254.169.253

- enableDNSHostname : by default is false
except for the  default vpc
 It won't do anything unless enableDnsSupport is true 

 If true it will assign public hostnames to ec2 instances if it has a public ip


 **If you use custom dns domain names in a private zone in route 53 you must set both these attributes to true**

**When we activate enableDNSHostname public dns names
will be attributed automatically to ec2 instances 
which have a public ipv4 address**

# Route 53 private zone

Create a private zone with an internal domain name
and attach it to 



# Network ACL and security groups 


## Security Groups :

- Each security group can have a maximum of 50 separate rules for inbound
and outbound.

- One security group can be attached to more than one EC2 instance.

- At any given time, a maximum of five security groups can be attached to a
network interface.

- When multiple security groups are attached to a single network interface,
rules from each security group are aggregated as one rule and evaluated
for any inbound or outbound traffic request.

- In situations where multiple rules for the same protocol and ports are given
for an EC2 instance, the most permissive rules are applied. For example,
say one rule allows communication on TCP port 22 (that is, SSH) from
10.108.123.65 , and another rule allows communication on TCP port 22
from everywhere, 0.0.0.0 . In that situation, communication from
everywhere is allowed.

- As soon as any modification in security group rules is saved, it
immediately gets applied to all associated EC2 instances.

- By default, when a security group is created, it allows all outgoing
communication and blocks all incoming communication. 

- In a security group, each rule is defined only to allow communication.
There is no provision in a security group to create a rule for explicitly
denying any traffic. The security group allows incoming traffic from all the
ports and protocols defined in the rules. All other traffic is implicitly
denied. This way, it eliminates the need for any explicit deny rule.

- Security groups are stateful. That means that any traffic allowed on a
specific inbound port and protocol is automatically allowed for outbound
traffic on the same port and protocol. You don't need to create outbound
traffic rules for ports and protocols described in inbound rules.


- Instances associated with the same security group cannot communicate
with each other unless there is a rule placed in any security group
associated with these instances that allows communication between these
instances.


## NACL 

NACL are lieke a firewall which control the traffic from and to the subnet

Default NACL allows everythin inbound and outbound


One NACL per subnet, new subnets are allowed the default NACL

Define NACL Rules :

Rules have numbers : 1-32766
 and higher precedence with lowest number


# VPC peering 

Allow to connect two VPC privately using AWS network

Makes them behave as if they were in the same network

Must not have overlapping ip addresses 

VPC peering connection is not transitive

We must update route table **in each vpc subnet** to ensure instances can communicate

VPC peering can work inter-region and cross-account

We can reference a security group of a peered vpc


Route tables must be modified on both subnets in order
to get the connexion working 


# VPC Endpoints :

VPC Endpoint is meant to access AWS Services
(Dynamo DB, S3 buckets, Cloudwatch) from private vpc
without going through the public www network

They scale horizontally and are redundant 

They remove the need of IGW, NAT, etc to access AWS services

2 types of VPC endpoints :

Interface :provisions an ENI (private ip address) as an entry point (must attach security group) : most AWS services


Gateway : provisions a target and must be used in a route table : S3 and Dynamo DB 

In case of issues : 
check DNS setting Resolution in your vpc 
check route tables 

Important :
After we configure vpc endpoint 

aws s3 ls does not work 

we have append the region in order to get access to the bucket 

by default aws cli is not configured on the instance
and therefore we have to add to add the region as an argument: 


aws s3 ls --region eu-west-3



# VPC Flow logs and Athena

3 types of flow logs :

VPC flow logs
Subnet flow logs
Elastic network interface flow logs


Helps to monitor & troubleshoot connectivity issue 

Flow logs data can go to S3  or cloudwatch logs 

Captures network information from aws managed interfaces too : ELB, RDS, Redshift , Workspaces






