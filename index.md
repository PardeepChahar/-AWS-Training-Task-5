# Welcome to AWS-Training-Task-5


## Task 5
Create High Availability Architecture with AWS CLI 
The architecture includes -
- Webserver configured on EC2 Instance
- Document Root(/var/www/html) made persistent by mounting on EBS Block Device.
- Static objects used in code such as pictures stored in S3
- Setting up Content Delivery Network using CloudFront and using the origin domain as S3 bucket.
- Finally place the Cloud Front URL on the webapp code for security and low latency.

### Step by step implementation

- #### Launch EC2 Instance
```
aws ec2 run-instances --image-id ami-00bf4ae5a7909786c --instance-type t2.micro --count 1
```
![image](https://user-images.githubusercontent.com/75135128/126551007-31ff7f9b-6eee-4351-bde4-2ccc76ed26dd.png)

- #### Status of EC2 Instance and number of EBS volume connected

![image](https://user-images.githubusercontent.com/75135128/126551947-659216c4-2d76-4afa-a078-1077ebc157c9.png)


- #### Create EBS volume

```
aws ec2 create-volume --availability-zone ap-south-1a --no-encrypted --size 1 --volume-type gp2
```
![image](https://user-images.githubusercontent.com/75135128/126551500-bb1f1ac5-06b7-4a78-94ab-77f9baa9b44f.png)

- #### Attach the created EBS volume to EC2

```
aws ec2 attach-volume --volume-id vol-0b30b9aff48c7c21d --instance-id 	i-0d225869f65243b82 --device /dev/sdh
```
![image](https://user-images.githubusercontent.com/75135128/126552061-41ec380e-51b8-496e-8fa6-d523c6120cdf.png)

- #### Check number of EBS Volumes connected

![image](https://user-images.githubusercontent.com/75135128/126552160-e1148c53-9a49-4fb9-8360-80d352cbe29a.png)

- #### Configure Apache Web Server
```
sudo yum install httpd -y
```
![image](https://user-images.githubusercontent.com/75135128/126554927-e13d7f2d-089c-4f48-ad45-193e2992364e.png)

- ##### Confirm installation
```
rpm -q httpd
```
![image](https://user-images.githubusercontent.com/75135128/126555056-27c647da-4595-4d48-a219-9ed82da72400.png)

- #### Start webserver and hit your webserver with your public ip
```
systemctl start httpd
```
![image](https://user-images.githubusercontent.com/75135128/126555444-bc8d29e4-af1f-40b9-b061-5958587d8a3c.png)

- #### Create S3 bucket
- Bucket Name: aws-t5-bucket
```
aws s3 mb s3://aws-t5-bucket --region ap-south-1
```
![image](https://user-images.githubusercontent.com/75135128/126556354-3929ce8d-dbb6-440a-8ec3-334db58b40e9.png)

- #### Confirm from AWS console
![image](https://user-images.githubusercontent.com/75135128/126556496-9b1ff812-0c8d-4d63-a8e8-add500874299.png)

- #### Upload an image to S3 bucket
```
aws s3 cp F:\RHEL\JenkinsCertificate.png s3://aws-t5-bucket
```
![image](https://user-images.githubusercontent.com/75135128/126557144-4b4102b5-cbea-4108-9641-2d5aca73c428.png)

- #### Confirm from AWS console
![image](https://user-images.githubusercontent.com/75135128/126557277-dcbf761f-7a34-4228-bf23-ef49962ead02.png)

- #### Change permission to Everyone (public access)
![image](https://user-images.githubusercontent.com/75135128/126557471-22a1490e-ba1c-4dc2-aa61-ec03d635d6ee.png)

- #### Create CloudFront distribution
```
aws cloudfront create-distribution --origin-domain-name aws-t5-bucket.s3.ap-south-1.amazonaws.com --default-root-object index.html
```
![image](https://user-images.githubusercontent.com/75135128/126558393-a8e9e0bb-1cc9-4e1e-9d11-7a91d3b730db.png)

- #### Confirm from AWS console
![image](https://user-images.githubusercontent.com/75135128/126558475-d31aba0f-5036-403b-bb5c-2fde24c06b1b.png)

- #### Now its time to mount EBS
- ##### Check total disk attached 
``` fdisk -l
```
![image](https://user-images.githubusercontent.com/75135128/126558989-8b6318f3-6da6-4bf1-8b79-749f893a3c44.png)

- ##### Create partition
![image](https://user-images.githubusercontent.com/75135128/126559141-44170818-721d-4078-9887-a4d7c3877da1.png)


- ##### Confirm partition
![image](https://user-images.githubusercontent.com/75135128/126559425-32bd32cc-0eef-45c6-af1a-a4a68e6d2fd5.png)

- ##### Format the partition
```
mkfs.ext4 /dev/xvdh1
```
![image](https://user-images.githubusercontent.com/75135128/126559666-2c6dda65-6aa1-44c0-b484-1e66920d525b.png)

- ##### Mount the EBS at /var/www/http
```
mount /dev/xvdh1 /var/www/html
```
![image](https://user-images.githubusercontent.com/75135128/126559943-23d2e946-7718-4805-bdd1-3e4e2efca5bb.png)

- #### Create an index.html file in  /var/www/html
```
vim index.html
```
- ##### Content of file
```
<body>
<h1 style="text-align:centre">This is Task 5 of AWS Training    **High Availability Architecture with AWS CLI**</h1>                                                 
<img src="Distribution domain name/img name" width="1100" height="600" />
</body>
```
![image](https://user-images.githubusercontent.com/75135128/126561752-67a4ca7e-fcca-467f-8dfd-9d3f550b5529.png)

- #### Hit your webserver
![image](https://user-images.githubusercontent.com/75135128/126561935-3f3b14c6-f370-4b79-8316-5d08887dbd7d.png)


- #### validate your image is comming from S3 or CloudFront( inspect the page and check the image url) 
![image](https://user-images.githubusercontent.com/75135128/126562145-7886ee27-ecd1-4499-80f7-c6d04ef7fb12.png)


And thus, all the objectives of the task are completed successfully.

![image](https://user-images.githubusercontent.com/75135128/126562489-cb1f73b0-bce4-49c8-9793-fe81c8bbb32c.png)

Thanks for reading the Article.
