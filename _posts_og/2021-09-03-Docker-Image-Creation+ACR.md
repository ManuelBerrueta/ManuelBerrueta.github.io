# Creating Docker Image and Azure Container Registry (ACR)
This post is more of a reference/notes on how to create an image, create an ACR, and push and pull the images

Some quick notes about container nomenclature... In a nutshell, the difference between an `image` and a `container` is that a `container` has a writing layer on it and so it follows that when you are running the `image` it is now a `container`.

## First we need to download and install Docker
- Download and install Docker: https://www.docker.com/get-started

### Create Azure Container Registry
First either create a resource group (you could also use an existing one):
`az group create --name <rgName> --location <chooseRegion> --subscription <subID>`

Now create your ACR:
`az acr create --name <acrName> --location <chooseRegion> --subscription <subID> --resource-group <rgName-FromAboveStep> --sku Premium`
**Note**: you can also use any of the following sku `Basic, Classic, Premium, Standard` sku

## Login to your ACR
If you don't have your username and password:
`az acr credential show --name <acrName> --subscription <subID> --resource-group <rgName-FromAboveStep>`

### Login
`az acr login --name <userName>`
**Note**: You cna enabled admin account for your ACR by running the command `az acr update --name <acrName> --subscription <subID> --resource-group <rgName-FromAboveStep> --admin-enabled true`

## Now lets go over creating an image:
### Creating a Dockerfile
To create our image, we can leverage a `Dockerfile` where we define the layers. You can think of the `Dockerfile` as the recipe for your `image`. With each component being a layer.
We start with our basic layer with a `FROM`, in our example bellow we are using Ubuntu 18.04. I want to note that you can go beyond this and create your base image from scratch, but this is beyond our needs at this time.
Next we `COPY` our target directory with the contents that we need to include in our image. This may be binaries, configuration files, scripts, you name it.
After, in the `RUN` I am telling Docker that I want to do a `apt-get update` and `install sudo`. This is because some of the binaries that I am running need to run as `root`.
Last, I want the container to run something as soon as the container is launched. I used `CMD` which tells Docker to run the whatever commands follows, in my case I want it to run a script located in the directory that we copied over at `/<DirToCopy>/my_script.sh`
I like wrapping up my commands in scripts that way I don't have to stack commands on top of each other in a Dockerfile and I find it easier to keep track of things and update as I need. 

Here is our simple Dockerfile:
```Docker
FROM ubuntu:18.04
COPY . /<DirToCopy>
RUN apt-get update && \
      apt-get -y install sudo
CMD /<DirToCopy>/my_script.sh
```

- https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

### Building our Image Using Docker
If we are in the same directory as our Docker file is as simple as using the following command:
```sh
sudo docker build -t <imageName>:1.0 .
```
With this command I am tagging my image, essentially giving the image a name an a version number.

We can test the image with:
```sh
docker run --name <nameForTestingLocally> <image_ID>
```
**NOTE:** The output of `docker build` will have the <image_ID>.

### Building our Image Using ACR
`az acr build --registry <acrName> --image <imageName>:<yourTag> --resource-group <rgName> --subscription <subID>`
**Note**: This builds, tags and pushes the newly created image to the registry in one command.

## Push + Pull Image to ACR
### Retag image for ACR
`sudo docker tag <imageName>:1.0 <ACR_Name>.azurecr.io/public/<imageName>`

### Push image to ACR
`docker push <ACR_Name>.azurecr.io/public/<imageName>`

Verify that your image made it to the ACR via the Portal or list it via:
`az acr repository list --name <acrName> --subscription <subID> --resource-group <rgName>`

### Pull image from ACR
`docker pull <ACR_Name>.azurecr.io/public/<imageName>`

### Run the image locally
`docker run -it <ACR_Name>.azurecr.io/public/<imageName>`

### Run the instance in Azure Container
`
az container create \
    --resource-group <rgName> \
    --name <containerName> \
    --image <ACR_Name>.azurecr.io/public/<imageName> \
    --registry-login-server <ACR_Name>.azurecr.io \
    --ip-address Public \
    --location <regionName> \
    --registry-username <userName> \
    --registry-password <passwd>
`

**Reference:** 
- https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli?tabs=azure-cli
- https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-azure-cli
- https://docs.microsoft.com/en-us/cli/azure/acr?view=azure-cli-latest
- https://docs.microsoft.com/en-us/learn/modules/build-and-store-container-images/?source=learn