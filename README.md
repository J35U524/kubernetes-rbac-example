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

 Print the certificate and code it in base64.
```
 $ cat practicioner.csr | base64 | tr -d '\n'
   LS0tLS1CRUd...IFJFUVVFU1QtLS0tLQo=
```
    
Assign the value to the <b>request</b> field in the <b>kubernetes-files/signing-request-template.yaml</b> file:
```
  apiVersion: certificates.k8s.io/v1beta1
  kind: CertificateSigningRequest
  metadata:
    name: practicioner-csr
  spec:
    groups:
    - system:authenticated
    request: LS0tLS1CRUd...IFJFUVVFU1QtLS0tLQo=
    usages:
    - digital signature
    - key encipherment
    - client auth
```
Generate the certificate signing request object.
```
 $ kubectl create -f kubernetes-files/signing-request-template.yaml 
   certificatesigningrequest.certificates.k8s.io/practicioner-csr created
```
List the certificate signing request objects. It shows a <b>Pending</b> condition.
```
 $ kubectl get csr
 NAME               AGE     SIGNERNAME                     REQUESTOR       CONDITION
 practicioner-csr   7m33s   kubernetes.io/legacy-unknown   minikube-user   Pending
```
Approve the certificate signing request object. After that list the certificate singning request objects again. It shows both <b>Approved</b> and <b>Issued</b> conditions:
```
 $ kubectl certificate approve practicioner-csr
   certificatesigningrequest.certificates.k8s.io/practicioner-csr approved
 
 $ kubectl get csr
  NAME               AGE   SIGNERNAME                     REQUESTOR       CONDITION
  practicioner-csr   15m   kubernetes.io/legacy-unknown   minikube-user   Approved,Issued
```


Get the certificate signing request <b>practicioner-csr</b>, extract the aproved <b>certificate</b>, decode it with base64 and save it as a cert file.  After that print the certificate file:
```
 $ kubectl get csr practicioner-csr -o jsonpath='{.status.certificate}' | base64 --decode > practicioner.crt

 $ cat practicioner.crt
   -----BEGIN CERTIFICATE-----
   MIIC/jCCAeagAwIB........
   .............
   .............
   .......UeSO6ahIvYTA=
   -----END CERTIFICATE-----
```