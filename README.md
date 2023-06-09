# kubernetes_project
This is an node js application deployed in Kubernetes 

As the first step we need to create the docker image and push to docker hub 

# Steps for creating docker image 

In the current working directory where we have dockerfile run the below commands 
$docker build -t shikhara1997/nodeapp . ( Here, shikhara1997 is my docker hub username) 
$docker run -d -p 8888:8080 shikhara1997/nodeapp ( This is to run the image locally to check we got the expected output. For checking we need give URL: http://localhost:8888 ) 
$ docker login 
$docker push shikhara1997/nodeapp

Now we have pushed our image to docker hub. 

# Next, we need to create a object ( deployment) in kubernetes cluster and access our application ( image ) 
For that install minikube and Kubectl on our machine. 
After installing verify the kubectl and minikube 
$minikube start 
$minikube status
![image](https://github.com/shikharaa/kubernetes_practice-/assets/28976807/645c4e60-9917-48d0-9311-efbb3af922ec)

$kubectl apply -f deployment.yml 
$kubectl get pods 
$kubectl get deployment
After deoloyment is running we should create service
$kubectl apply -f deploymentservice.yml 
$kubectl get svc 
![image](https://github.com/shikharaa/kubernetes_practice-/assets/28976807/9d94f998-a1ad-4d6a-9418-9489f797a4bf)
$minikube service nodeapp-service ( To get the URL to access the application ) 
![image](https://github.com/shikharaa/kubernetes_practice-/assets/28976807/bfdea2e2-4c2b-4b84-915d-1d4151676f60)

Other way to run the app without service.yml file is to
$kubectl expose deployment/nodeapp-deployment
$kubectl proxy 
![image](https://github.com/shikharaa/kubernetes_practice-/assets/28976807/e215d7b2-e740-4cf5-a9d7-6d35a3709b71)

The access the URL: http://127.0.0.1:8080 

# Scaling the application using a ReplicaSet
Use the scale command to scale up your Deployment.
$kubectl scale deployment nodeapp-deployment --replicas=3 ( Here we are increasing the pods) 
![image](https://github.com/shikharaa/kubernetes_practice-/assets/28976807/58ab11a9-d466-44e9-8bc4-03a34cba9e21)
We can also scale down the pods 
![image](https://github.com/shikharaa/kubernetes_practice-/assets/28976807/bf2c3e2b-2bc9-4c0a-8908-5163bddbaa52)

# Perform rolling updates
Now we can create the another image by making the changes in the existing image like changing the log statement. Herewe are using the config image ( see below) 
Edit the deployment.yml file with new image ( nodeapp-config) 
Then, apply the deployment.yml file 
![image](https://github.com/shikharaa/kubernetes_project/assets/28976807/a4740daf-ad0b-4d7c-a1eb-2bc01d2a05d3)
Now config app is running
Then rollout to older image 
![image](https://github.com/shikharaa/kubernetes_project/assets/28976807/b9b7f389-ac66-4c81-8372-83817c4c75fe)
Now original version is running

# Using a ConfigMap to store configuration
Make changes in the app.js for getting value from config map 
res.send(process.env.MESSAGE + '\n') 
Inside the deployment.yml file add a descriptor to take the values from configMap 
envFrom:
        - configMapRef:
            name: app-config
            
Now create the configMap using 
$kubectl create configmap app-config --from-literal=MESSAGE="This message came from a ConfigMap!"
Here app-config name should be same in deployment file and string literal 
![image](https://github.com/shikharaa/kubernetes_project/assets/28976807/0ccb1242-066e-40d4-817c-208395118f5a)

$kubectl apply -f deployment.yml
![image](https://github.com/shikharaa/kubernetes_project/assets/28976807/6a6ecf33-83b8-498b-b5d8-c948b02b1534)
Now run it 
![image](https://github.com/shikharaa/kubernetes_project/assets/28976807/4aa07a0f-6b74-488a-b560-58a9b42e45be)
![image](https://github.com/shikharaa/kubernetes_project/assets/28976807/23ee68dd-ed3f-405b-b046-c77b69022a95)

