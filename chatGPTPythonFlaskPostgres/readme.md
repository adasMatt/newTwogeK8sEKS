# Simple application deployment by Mattyyy with assistance of chatGPT

- create python-flask application and build docker image:  
-had to figure out environment variables  
-had to figure out requirements.txt  
-not sure if 2nd line for RUN part of Dockerfile is necessary (might be redundant with something in requirements.txt) but haven't had time to double check  
    
- create ns and configmap
    - don't think DB_USER and DB_PASSWORD are necessary in configmap, they are pulled from secrets 
    - 
- create db deployment and service
    - did not change anything. chatGPT slams 
    - 
- create app deployment and service
    - chatGPT forgot to write service, but that's an ez file
    - 
- forward local traffic to the app via:  
`minikube kubectl -- port-forward service/app-service 5000:5000 -n my-app`

- I forgot to use resourcequota in this, which that should be a simple fix but I've gone down this rabbit hole enough for now and should get back to the main project or write and architecture diagram for this.

Images of project working:  
![image1](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/images/chatGPTPyFlWebBrowser.png "app shown in web browser on localhost:5000")
![image2](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/images/chatGPTPythonFlaskPostgres.png "terminal output of application running and accessible through localhost:5000")



PS: The bulleted list layout is dumb imo. The spacing between the indented list and the next parent bullet are offending me. Why are you like this markdown? 
