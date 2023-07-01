# Terraform-setup-Wordpress-securely-in-a-custom-aws-vpc

# *Provider*

```
provider "aws" {

  region     = "ap-south-1"
  access_key = "Your_access_key"
  secret_key = "Your_secrey_key"
}
```

# *Data Resource*

```
data "aws_route53_zone" "selected" {
  name         = "learndevops.website"
  private_zone = false
}

data "aws_availability_zones" "available" {
  state = "available"
}
```

# *Variable*

```
variable "project_name" {

  description = "my project name"
  type        = string
  default     = "zomato"
}

variable "project_environment" {

  description = "my project environment"
  type        = string
  default     = "prod"
}

variable "instance_type" {

  description = "my instance type"
  type        = string
  default     = "t2.micro"
}

variable "ami_id" {

  description = "my instance ami id"
  type        = string
  default     = "ami-057752b3f1d6c4d6c"
}

variable "vpc_cidr_block" {

  type    = string
  default = "172.16.0.0/16"
}

variable "main_cidr_block" {

  default = "172.16.0.0/16"
}

variable "region" {

  type    = string
  default = "ap-south-1"
}

variable "private_zone_name" {

  type    = string
  default = "learndevops.local"
}

variable "enable_natgw" {
  type    = bool
  default = false #or false
}
```

# *Output*

```
output "frontend_public_ip" {
  value = aws_instance.frontend.public_ip

}

output "frontend_private_ip" {

  value = aws_instance.frontend.private_ip


}

output "frontend_public_dns" {

  value = aws_instance.frontend.public_dns

}

output "frontend_url" {

  value = "http://${aws_instance.frontend.public_dns}"

}


output "aws_security_group_frontend" {
  value = aws_security_group.frontend.id

}

################

output "bastion_public_ip" {
  value = aws_instance.bastion.public_ip

}

output "bastion_private_ip" {

  value = aws_instance.bastion.private_ip


}

output "bastion_public_dns" {

  value = aws_instance.bastion.public_dns

}


output "aws_security_group_bastion" {
  value = aws_security_group.bastion.id

}

####################


output "backend_private_ip" {

  value = aws_instance.backend.private_ip


}

output "bastion_private_dns" {

  value = aws_instance.backend.private_dns

}


output "aws_security_group_backend" {
  value = aws_security_group.backend.id

}

########################################


#cidrsubnet output

output "main_cidr_block_subnet1" {

  value = cidrsubnet(var.main_cidr_block, 3, 0)


}

output "main_cidr_block_subnet2" {

  value = cidrsubnet(var.main_cidr_block, 3, 1)


}
output "main_cidr_block_subnet3" {

  value = cidrsubnet(var.main_cidr_block, 3, 2)


}
output "main_cidr_block_subnet4" {

  value = cidrsubnet(var.main_cidr_block, 3, 3)


}

output "main_cidr_block_subnet5" {

  value = cidrsubnet(var.main_cidr_block, 3, 4)


}
output "main_cidr_block_subnet6" {

  value = cidrsubnet(var.main_cidr_block, 3, 5)


}


########################################

#Route53 public hosted zone

output "details_of_zone" {
  value = data.aws_route53_zone.selected.id
}


#Availability zone names

output "details_of_availability_zone" {
  value = data.aws_availability_zones.available.names
}
```

# *Wordpress userdata*

```
#!/bin/bash


yum install httpd php php-mysqlnd -y
systemctl restart httpd php-fpm
systemctl enable httpd php-fpm

cd /tmp/
wget  https://wordpress.org/latest.tar.gz
tar -xvf latest.tar.gz
mv wordpress/* /var/www/html/
chown -R apache: apache /var/www/html/
cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php


sed -i "s/database_name_here/blogdb/" /var/www/html/wp-config.php

sed -i "s/username_here/wpuser/" /var/www/html/wp-config.php

sed -i "s/password_here/wpuser1234/" /var/www/html/wp-config.php

sed -i "s/localhost/backend.learndevops.local/" /var/www/html/wp-config.php

rm -rf wordpress
```

# *Mysql userdata*

```
#!/bin/bash


yum install mariadb105-server -y
systemctl restart mariadb.service
systemctl enable mariadb.service

mysql -u root -e "create database blogdb;"

mysql -u root -e "create user wpuser@'%' identified by 'wpuser1234' ;"

mysql -u root -e "grant all privileges on blogdb.* to wpuser@'%';"

mysql -u root -e "flush privileges;"
```

# *Main*

```

```
