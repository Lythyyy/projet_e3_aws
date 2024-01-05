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

  ![s1](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/c28bff0a-7432-46f7-9170-e7984b1227ee)

  ![s2](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/0604233b-3d0e-4c99-8443-f165acf04730)

  ![s3](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/46ed64a7-6308-41d3-bc52-03c8b5a9a2ad)
 
# Étape 5 : Création d'une Internet Gateway pour la VPC 1

- Cette Internet Gateway permet au VPC et donc à l'instance d'accéder à Internet.

  aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text => Crée la passerelle

  aws ec2 attach-internet-gateway --vpc-id vpc-07d0635eb01d2f310 --internet-gateway-id igw-01fbbebbdaedbb7bf => L'attache au VPC 1

  ![gateway](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/b2c63c09-f23d-4c46-89e2-e44824c95ce7)

# Étape 6 : Créer des tables de routage

- Cette étape primordiale permet aux sous-réseaux de "se repérer" plus facilement dans le réseau et pour être correctement redirigé selon les requêtes.

  aws ec2 create-route-table --vpc-id vpc-07d0635eb01d2f310 --query RouteTable.RouteTableId --output text

  aws ec2 create-route-table --vpc-id vpc-08a66e1e14f613367 --query RouteTable.RouteTableId --output text

  aws ec2 create-route-table --vpc-id vpc-069c8454e16476006 --query RouteTable.RouteTableId --output text

- Puis on les associe à leur sous-réseau.

  aws ec2 associate-route-table --route-table-id rtb-0f96d5cb119f07a45 --subnet-id subnet-0f5dcd14716c25dae

  Valable pour les 2 autres également.

  ![tables](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/aa68ecf9-e435-4446-8f69-2df4e8ae9c52)

# Étape 7 : Création des connexions d'appairage entre VPC

- Les connexions d'appairage permettent à deux VPC de communiquer plus facilement entre eux.

  aws ec2 create-vpc-peering-connection --vpc-id vpc-07d0635eb01d2f310 --peer-vpc-id vpc-08a66e1e14f613367 --region us-east-2 => Peer entre VPC 1 et 2

  aws ec2 create-vpc-peering-connection --vpc-id vpc-07d0635eb01d2f310 --peer-vpc-id vpc-069c8454e16476006 --region us-east-2 => Peer entre VPC 1 et 3

  aws ec2 create-vpc-peering-connection --vpc-id vpc-08a66e1e14f613367 --peer-vpc-id vpc-069c8454e16476006 --region us-east-2 => Peer entre VPC 2 et 3

  ![peer](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/597439cd-716f-4b58-8ec7-757dc76d8162)

  - Il est ensuite nécessaire de valider l'appairage.
 
  aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id pcx-033812733a6fd7cde => commande effectuée pour les trois appairages.

# Étape 8 : Mise à jour des tables de routage pour les connexions d'appairage

  - Après avoir créé les connexions d'appairage, il est important de les intégrer dans les tables de routage.
 
    aws ec2 create-route --route-table-id rtb-057ba73482bae5baa --destination-cidr-block 10.1.1.0/24 --vpc-peering-connection-id pcx-033812733a6fd7cde => à faire pour les 3 VPC

# Étape 9 : Lancement des instances

- Les instances sont lancées avec Debian.

  aws ec2 run-instances --image-id ami-05fb0b8c1424f266b --count 1 --instance-type t2.micro --subnet-id subnet-0f5dcd14716c25dae --security-group-ids sg-0a391690db32fe586

- Elles sont chacune associées à leur subnet et à leur groupe de sécurité.

# Étape 10 : Création du bucket S3

- Le bucket permet de stocker des fichiers accessibles via un lien.

  aws s3 mb s3://bucket-scharff => Crée un bucket

  aws s3 cp path/image.jpg s3://bucket-scharff/ => Upload une image dans le bucket

  aws iam create-role --role-name MonRole --assume-role-policy-document file://TrustPolicy.json => Crée un rôle IAM

  ![trust](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/e720a624-c667-4f50-bf1f-de139bea73dc) Le fichier TrustPolicy.json


  aws iam attach-role-policy --role-name MonRole --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess => Attache une politique au rôle

  aws iam add-role-to-instance-profile --instance-profile-name MonInstance --role-name MonRole => Ajoute le rôle au profil d'instance

  aws ec2 associate-iam-instance-profile --instance-id i-0e3a8169bd9697b1d --iam-instance-profile Name=MonRole => Associe le rôle IAM à l'instance

  ![buck](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/4ffcb06f-9f9d-4ede-baa7-c8e775169f71)

# Étape 11 : Installation d'un serveur web sur l'instance 1 et configuration d'Apache2

- Les commandes suivantes permettent d'installer le serveur web Apache2.

  sudo apt update

  sudo apt install apache2

  cd /var/www/html/

  sudo nano index.html

  ![code](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/361b4a08-75af-4ab7-90c2-9408eac23c67)

  sudo systemctl apache2 enable

  ![site](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/c457db83-cec1-4b15-a558-bc5472eb600d)

# Étape 12 : Créer un bucket pour l'hébergement statique

- Cet hébergement permet de stocker les documentations voulues.

  aws s3api put-bucket-website --bucket bucket-scharff2 --website-configuration file://website-configuration.json

  ![config](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/280a5e4a-8e2f-42f5-bd50-b6ed5871a7ed) le website-configuration.json

  aws s3 sync /chemin/site s3://bucket-scharff2/ => pour lier le chemin

  ![static](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/c29d0ba9-6768-4459-824a-a09f175090c5)

  ![bucke](https://github.com/Lythyyy/projet_e3_aws/assets/107408392/aefd5b4f-8eca-461d-9945-ce62b18d050f)

