# kubernetes Role-based Access Control
Exercise for Authentication and Authorization  


This exercise  was tested in a environment with the next specs.

        Minikube v1.13.1
        Kubernetes v1.19.2
        Docker version 19.03.13-ce

Start Minikube:
```
 $ minikube start
```

Create the <b>rbacexample</b> namespace:
```
 $ kubectl create namespace rbacexample
 namespace/rbacexample created
```
Create rbac-example directory and cd into it:
```
 $ mkdir rbac-example
 $ cd rbac-example/
```

With the openssl tool, create a private key for the <b>practicioner</b> user, after that create a certificate signing request for the practicioner user:
```
 $ openssl genrsa -out practicioner.key 2048
   Generating RSA private key, 2048 bit long modulus
   .................+++
   ..........................................+++
   e is 65537 (0x10001)

 $ openssl req -new -key practicioner.key -out practicioner.csr -subj "/CN=practicioner/O=learner"
```

 Print the certificate and code it in base64, and assign it to the request field in the signing-request.yaml file:
```
 $ cat practicioner.csr | base64 | tr -d '\n'
   LS0tLS1CRUd...IFJFUVVFU1QtLS0tLQo=
```
    

