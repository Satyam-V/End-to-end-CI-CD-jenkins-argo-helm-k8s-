In this project :
The Flow of our CI-CD pipeline goes like:

First we will talk about continuous integration:

Jenkins(as continuous integration )- Github-webhook(to trigger the pipeline defined in jenkinsfile) - Maven(to build the code into deliverables i.e. artifacts) - Sonar (for static analysis of code)- Docker(to create an image out of it) - DockerHub(as a container registry) [once done with all the steps, continuous integration is successful.]
Explanation:
1st step:
we use github-webhook to trigger the pipeline once the code is pushed to github (you can make the configuration as per your need like you want to trigger it when code is pushed or once the pull request raised by developers is approved.) 
Note: Also you can make use jenkins for periodic build triggers or scm pull based mechanism, using webhook is better use case.
2nd step:
once jenkins got notified it will perform the steps based on the stages we have defined in our jenkinsfile (as we are declarative pipeline approach to define pipeline and its stages).
Note: We have to configure the jenkins pipeline job for the same either you can do it via jenkins UI (as it has very interactive UI and even you can make it more better using pluggins like Blueocean or you can also make use of jenkins CLI).
3rd step:
Maven uses pom.xml file and will perform the defined junit-testcases and build the artifact out of the raw code to convert it into deliverables i.e. jar or war file.
Note:
If the build is failed  you can configure alerts based on that, in jenkins we have EMAIL pluggins or you can send out slack notifications using api's about the failed state. 
4th step:
we will integrate sonarqube for static analysis of code i.e. is there any security vulnerabilities are there or to perform checks like code smells, code quality based on quality profiles and quality gates you have set for your project in sonarqube.
5th step:
Next, We will build the image out of the docker file using shell command which we have stored in the same project directory.
6th step:
It is the final step of our continuous integration in which we just push the built image to container registry for versioning. In my case I have used Dockerhub as contaner registry you can also make use of other registries such as ECR and all.
Some Important Notes regarding Continuous integration:
i. All These continuous integration steps are defined in jenkinsfile.
ii. We are using Declerative way to write our Jenkinsfile as it is more structure than scripted way, more easy to write (unless you dont have more experienced groovy developers in your team you can make use of this declerative pipelines. )
iii. Also, to run different stages defined in your declerative pipeline you can make use of single server to install and configure all the tools but it is better to use docker agents or nodes and make use of those agents.(as docker agents are light weight in nature and will require less configurations.)   

For continuous delivery and deployment we are making use of Gitops based solution i.e. Argo-Cd which is a continuous delivery platform. see below how the flow goes:

Argo image updater (will continuously check for updated image in container registry) -Another new GitHub Repository(image updater will pick the updated image from registry and updates it in New Repo)- ArgoCd Gitops Solution (will fetch the updated image in the git repository and Deploy it on Kubernetes Cluster and this new git repository will contain the files related to apllication manifest i.e pod.yaml or deployment.yaml).
Note: 
i. This Argo image updater and ArgoCd tool we install it in Our Kubernetes Cluster.
ii. The another git repository we are using is for image manifest that is our Helm charts or customized pod.yaml or Deployment.yaml.

Explanation:
Once the Continuous integration process complete Image is pushed into container registry as the final stage of jenkins pipeline, Image updater deployed in k8s cluster will fetch the updated image and updates it to (2nd)Git Repository and ArgoCD will fetch the change in Git repository and will deploy it on K8s cluster. This completes our continuous delivery process.




