# Sample Wordpress Deployment

## First Create a secret for the MYSQL Database
   ```
   kubectl create secret generic mysql-pass --from-literal=password=$(head -c 24 /dev/random | base64)
   ```
## Second pick the storage backend
For Azure files (storage account with File share)

    ```
    kubectl apply -f 1-Deploy Storage using AzureFiles.yaml
    ```
## For a Managed Disk (disk mounted to the Virtual Machine)

    ```
    kubectl apply -f 1-Deploy Storage using Managed Disk.yaml
    ```

## Then apply the remaining manifest yaml files in number order
    ```
    kubectl apply -f 2-Deploy MySQL DB Backend.yaml
    kubectl apply -f 3-Deploy MySQL DB Backend ClusterIP Service.yaml
    kubectl apply -f 4-Deploy Wordpress Application.yaml
    kubectl apply -f 5-Deploy Wordpress Application LoadBalancer Service.yaml
    ```
### This will make it so that your App is available on port 80 (http) over a public ClusterIP

## Optionally, configure Azure Application Gateway Ingress controller to make the app available over HTTPS

    ### Follow this guide:
    https://docs.microsoft.com/en-us/azure/application-gateway/ingress-controller-overview

    ### Obtain an https certificate that machines the desired name.
    Example: https://letsencrypt.org/getting-started/

    ### Create a TLS secret to store your https certificate public and private key
    
    ```
    kubectl create secret tls star-felizlabs --key .\wildcard_felizlabs_xyz.key --cert .\wildcard_felizlabs_xyz.cer
    ```
    
    ### Update the manifest 6-Deploy Wordpress Application Ingress for AppGW and HTTPS.yaml with the appropiate information.

    ### Apply it:
    ```
    kubectl apply -f 6-Deploy Wordpress Application Ingress for AppGW and HTTPS.yaml
    ```
