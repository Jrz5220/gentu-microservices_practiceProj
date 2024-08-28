create a UK University information application composed of two microservices on IBM Code Engine, and how to scale and update each microservice independently of the other.

# Deploy the Microservices
Change to the universities directory to see two directories listed:
- listing - contains the microservice that will list all universities or a list of universities based on keyword search.
- websites -  contains the microservice that will provide the list of websites given a college name.

Run the following command to deploy listing as a microservice
- ibmcloud ce app create --name listing --image us.icr.io/${SN_ICR_NAMESPACE}/listing --registry-secret icr-secret --port 5000 --build-context-dir listing --build-source .
- --name: name of your application
- --image: the image to reference
- --registry-secret: registry secret to access the non-public container registry

Now run the following comand to get the details about the app. This will also list a URL under "URL: " that you can access the endpoints through.
- ibmcloud ce app get -n listing

Fetch all the colleges which have the name Trinity in it using the URL from above
- curl <deplymenturl>/colleges/Trinity

Run the following command to deploy the websites as a microservice
- ibmcloud ce app create --name websites --image us.icr.io/${SN_ICR_NAMESPACE}/websites --registry-secret icr-secret --port 5000 --build-context-dir websites --build-source .

Fetch all the websites for the college Trinity College of Music
- curl <deplymenturl>/websites/Trinity%20College%20of%20Music
- %20 is URL encoding for blank space

# Scale and Update the Microservices
Scale the microservices and/or update them independently without affecting the whole application, having to redeploy it or restart it.
Imagine two scenarios:
1. The listings API endpoint gets a lot of hits and has to be scaled.
2. After creating the websites API endpoint, based on user experience, you decide to offer a keyword search instead of an exact word search.

Run the following command to scale the listing application to three instances
- ibmcloud ce app update --name listing --min 2

Run the following command to see if the listing application has scaled
- ibmcloud ce app get --name listing -q
- Wait for the application to scale and all instances to run

## Updating the Microservice
Run the following command to update the website application after updating the code in app.py to take keyword search as a parameter instead of giving the whole college name
- ibmcloud ce app update --name websites --image us.icr.io/${SN_ICR_NAMESPACE}/websites --registry-secret icr-secret --port 5000 --build-context-dir websites --build-source .

Verify the application was updated by testing the URL
- curl <deplymenturl>/websites/Trinity
