# Deploying-a-Web-Application-on-Kubernetes
## step-1 Generate Base64-Encoded Credential
we encoded our secrets using base64 by running 
echo -n 'my-admin-sivanithin-user' | base64
echo -n 'my-super-secret-sivanithin-password' | base64
![image](https://github.com/user-attachments/assets/f502dc68-fdc4-45af-bea0-6ee94d823c87)

## step-2 Define the Secret in a YAML file
### we generated the encoded credentials for using in Secret.yml file
### the secret allows environment variables in the MongoDB and Mongo-Express containers to pull credentials securely without coding them directly in configuration files
### after creating the file ,we run kubectl apply -f mongo-secret.yaml
### by running this the   Kubernetes will create the secret.yml file
### to check whether it create or not kubectl get secret we run "kubectl get secret"

## step-3 Step 2: Deploy MongoDB using a Deployment and Service
### We define the deployment and internal service in and apply:
### kubectl apply -f mongo-deployment.yaml
### This creates the MongoDB pod and an internal service.
##  Step 4: Create ConfigMap
### We store the MongoDB service address in a ConfigMap:
### kubectl apply -f mongo-configmap.yaml
##  Step 5: Deploy Mongo-Express and Expose it with an External Service
### We define the deployment and external service in mongo-express.yaml after this we apply 
### kubectl apply -f mongo-express.yaml
## step-6 Accessing and Testing Your Application
### we run minikube service mongo-express-service
![Screenshot (151)](https://github.com/user-attachments/assets/cc8e3d97-2d20-4b1c-bf79-e1b40c98db1d)
### after running this command the website will automatically open  in our browser
![Screenshot (142)](https://github.com/user-attachments/assets/b13c0fe4-fdb6-4993-bafb-6e533fda5eb7)

### the page will ask for credentials username and password 
### username:my-admin-sivanithin-user
### password:my-super-secret-sivanithin-password

### By default, the Mongo-Express web interface may show a login screen but fail to authenticate if basic authentication credentials are not explicitly defined. This can result in the login screen not responding or reloading after you enter your credentials.
###we used , two environment variables were added to the Mongo-Express Deployment:
```
- `ME_CONFIG_BASICAUTH_USERNAME`  
- name: ME_CONFIG_BASICAUTH_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username             
        
- `ME_CONFIG_BASICAUTH_PASSWORD`
- name: ME_CONFIG_BASICAUTH_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
```
### These variables enable HTTP Basic Authentication for the Mongo-Express UI itself and are separate from the MongoDB admin credentials. In this setup, the same values from the Kubernetes Secret used for database login were reused for simplicity.

### After adding these variables, the login functionality started working correctly, allowing secure access to the Mongo-Express dashboard.

## step-7 Log in and Test:
after logging in succesfully we tried to creat the new data base name "new_db"
![Screenshot (154)](https://github.com/user-attachments/assets/f440b126-a46d-4ccb-9d99-375570ddc87c)
created new collections in it "new_collections"
![Screenshot (153)](https://github.com/user-attachments/assets/8e522c75-79bd-4311-815b-2d9ff907bf04)
added a document inside this
![Screenshot (148)](https://github.com/user-attachments/assets/44e3c3f0-7c7e-42f5-b7da-60df6fe8a4ad)

##  Mongodb backend verification
### i ran this kubectl exec -it mongodb-deployment-6d9d7c68f6-kjm74 -- mongosh -u my-admin-sivanithin-user -p my-super-secret-sivanithin-password --authenticationDatabase admin
### This command allows us to log into the MongoDB server running inside the Kubernetes Pod, using the correct admin credentials, and interact with the database directly from the terminal.
![image](https://github.com/user-attachments/assets/40b6eb01-1a4d-4e15-9dbb-56e2d1fc1c47)
```
>test show dbs 
i used this command for getting all the dbs in it 
got "new_db" in the list whiich we created in website and used it by use new_db
test> use new_db
switched to db new_db
new_db> show collections
delete_me
new_collection
new_db> db.new_collection
new_db.new_collection
new_db> db.new_collection.find()
[
  {
    _id: ObjectId('68565983bbe7b01e56411511'),
    name: 'Siva Nithin',
    age: 22,
    email: 'siva@nithin.com'
delete_me
new_collection
new_db> db.new_collection
new_db.new_collection
new_db> db.new_collection.find()
[
  {
    _id: ObjectId('68565983bbe7b01e56411511'),
    name: 'Siva Nithin',
    age: 22,
    email: 'siva@nithin.com'
  }
]
```
![Screenshot (156)](https://github.com/user-attachments/assets/41a1f843-db23-428a-816c-6740df4b218a)
### by this we can conclude that 
### The document created via the Mongo-Express UI was successfully stored in the MongoDB backend.
### Verified using both web UI and backend shell access.
