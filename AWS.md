- PETITOT Olivier
- LORETTE Thibaut
- SCHARFF Léo

# COMPTE-RENDU PROJET AWS E3

![Projet AWS](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/8668431e-bc63-4f1e-896a-f45fda54ca5d)

# Étape 1 : Création des VPC

- C'est une étape primordiale dans la création d'une infrastructure AWS : les VPC sont des réseaux virtuels privés.
- Pour ce projet, il est nécessaire d'en créer 3.

  aws ec2 create-vpc --cidr-block 10.0.0.0/16 => Pour le 1er VPC

  aws ec2 create-vpc --cidr-block 10.1.0.0/16 => Pour le 2ème VPC

  aws ec2 create-vpc --cidr-block 10.2.0.0/16 => Pour le 3ème VPC

  ![vpc](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/0b4463cf-2f2c-4757-83af-b19d32357a37)

# Étape 2 : Création des Subnets pour les VPC

- Les subnets sont essentiels pour pouvoir créer différents sous-réseaux à l'intérieur même d'un VPC.
- Il faut en créer 1 par VPC, soit 3.

  aws ec2 create-subnet --vpc-id vpc-07d0635eb01d2f310 --cidr-block 10.0.1.0/24 => Pour le VPC 1

  aws ec2 create-subnet --vpc-id vpc-08a66e1e14f613367 --cidr-block 10.1.1.0/24 => Pour le VPC 2

  aws ec2 create-subnet --vpc-id vpc-069c8454e16476006 --cidr-block 10.2.1.0/24 => Pour le VPC 3

  ![subnet](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/fe27341c-efb9-412b-be7b-cf5ded2c59a5)

# Étape 3 : Création des groupes de sécurité

- Les groupes de sécurité permettent de contrôler le trafic entrant et sortant d'une instance.

  aws ec2 create-security-group --group-name security-1 --vpc-id vpc-07d0635eb01d2f310 --description "Security group 1"

  aws ec2 create-security-group --group-name security-2 --vpc-id vpc-08a66e1e14f613367 --description "Security group 2"

  aws ec2 create-security-group --group-name security-3 --vpc-id vpc-069c8454e16476006 --description "Security group 3"

  ![sec](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/7b95fe07-5d70-460a-b162-a72956a6dd5d)

# Étape 4 : Promulguer les autorisations à chaque groupe de sécurité

- Hormis pour le groupe de sécurité 1 qui est publique, les deux autres se voient attribuer des autorisations spéciales pour le trafic SSH et HTTP.

  aws ec2 authorize-security-group-ingress --group-id sg-0a391690db32fe586 --protocol tcp --port 22 --cidr 0.0.0.0/0 => Ouvre le port 22, correspondant au SSH

  aws ec2 authorize-security-group-ingress --group-id sg-0a391690db32fe586 --protocol tcp --port 80 --cidr 0.0.0.0/0 => Ouvre le port 80, correspondant au HTTP

  - Ces commandes sont appliquées aux deux groupes de sécurité pour les VPC 2 et 3.
 
# Étape 5 : Création d'une Internet Gateway pour la VPC 1

- Cette Internet Gateway permet au VPC et donc à l'instance d'accéder à Internet.

  aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text => Crée la passerelle

  aws ec2 attach-internet-gateway --vpc-id vpc-07d0635eb01d2f310 --internet-gateway-id igw-01fbbebbdaedbb7bf => L'attache au VPC 1

  ![gateway](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/b2c63c09-f23d-4c46-89e2-e44824c95ce7)

# Étape 6 : Associer les sous-réseaux et les tables de routage

- Cette étape primordiale permet aux sous-réseaux de "se repérer" plus facilement dans le réseau et pour être correctement redirigé selon les requêtes.

  
