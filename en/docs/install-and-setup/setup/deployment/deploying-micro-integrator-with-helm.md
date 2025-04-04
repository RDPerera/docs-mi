# Deploy the Micro Integrator on Kubernetes using Helm resources

Follow the instructions below to deploy the Micro Integrator on Kubernetes (K8s) using Helm resources.

## Before you begin
    
- Ensure you have an active [WSO2 Subscription](https://wso2.com/subscription). If you don't have a subscription, sign up for a [WSO2 Free Trial Subscription](https://wso2.com/free-trial-subscription).

    !!! Note
        You need an active subscription to use the updated Docker images of the Micro Integrator with your Helm resources. Otherwise, you can use the community version of the Docker images, which do not include product updates.
    
- Install [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), [Helm](https://helm.sh/docs/intro/install/), and the [Kubernetes client](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
    
- Set up a Kubernetes cluster. You can also try deploying Micro Integrator locally with a [local Kubernetes cluster](https://kubernetes.io/docs/tasks/tools/).
    
- Install the [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/deploy/). 

    !!! Note
        Helm resources for WSO2 product deployment patterns are compatible with the ingress-nginx [controller-v1.12.0](https://github.com/kubernetes/ingress-nginx/releases/tag/controller-v1.12.0) release.

## Step 1 - Get the Helm resources

Check out the Helm resources for the WSO2 Micro Integrator Git repository. WSO2 releases Helm resources for all product deployments.

1. Open a terminal and navigate to the location where you want to save the local copy.
2. Clone the Micro Integrator Git repository with Helm resources:

    ```bash
    git clone https://github.com/wso2/helm-mi.git
    ```

This creates a local copy of the [`wso2/helm-mi`](https://github.com/wso2/helm-mi/) repository, which includes all the Helm resources for WSO2 Micro Integrator.

Let's refer to the root folder of the local copy as `<HELM_HOME>`.

## Step 2 - Update the deployment configurations 

Follow the steps below to configure your Micro Integrator deployment.

1. Open the `values.yaml` file in the `<HELM_HOME>/mi` folder of your local copy.

2. Use the following guidelines to update the deployment configurations:

    - **Update the WSO2 subscription details**
    
        You can update the username and password in the following section. If you don't have an active WSO2 subscription or plan on using the community version of the Docker images, leave these parameters empty.
    
        ```yaml
        wso2:
            subscription:
                username: "<username>"
                password: "<password>"
        ```

        Alternatively, you can skip this step and pass your subscription details during deployment (see section [Update configurations during deployment](#update-configurations-during-deployment) for details).

    - **Update the Micro Integrator Docker images**

        By default, the `values.yaml` file uses the base Micro Integrator image (which does not include any integrations) to set up the deployment.

        ```yaml
        containerRegistry: "docker.wso2.com"
        wso2:
            deployment:
                image: 
                    repository: "wso2mi"
                    tag: "4.4.0"
        ```

        If you have a custom Docker image with integrations, update the `containerRegistry` parameter and provide the details of your custom image.

        ```yaml
        containerRegistry: "<docker_registry>"
        wso2:
            deployment:
                image: 
                    repository: "<custom_mi_repository>"
                    tag: "<custom_image_tag>"
        ```

    - You can update [other configurations](https://github.com/wso2/helm-mi/blob/main/mi/EXAMPLES.md) as needed.

3. Save the `values.yaml` file.

## Step 3 - Deploy the Micro Integrator

Once you have configured your Helm resources locally, follow the instructions below to deploy the Micro Integrator.

1. Open a terminal and navigate to the `<HELM_HOME>/mi` folder.
2. Run the command appropriate for your Helm version.

    !!! Tip
        Be sure to replace `NAMESPACE` with the Kubernetes namespace where your resources are deployed.

    - Using **Helm v3**:
        
        ```bash
        helm install <RELEASE_NAME> ./ -f values.yaml -n <NAMESPACE> --create-namespace
        ```

!!! Note
    For a pre-configured, easy setup for local deployment, use the `values_local.yaml` file.
        
#### Update configurations during deployment

If needed, you can set deployment configurations at the time of deployment instead of specifying them in the `values.yaml` file. See the examples below.

- Set the subscription username and password:

    ```bash
    --set wso2.subscription.username=<SUBSCRIPTION_USERNAME>
    --set wso2.subscription.password=<SUBSCRIPTION_PASSWORD>
    ```

- Set the custom Micro Integrator Docker image:

    ```bash
    --set containerRegistry=<CUSTOM_IMAGE_REGISTRY>
    --set wso2.deployment.image.repository=<CUSTOM_IMAGE_REPOSITORY>
    --set wso2.deployment.image.tag=<CUSTOM_IMAGE_TAG>
    ```

- Use the following parameter only if your custom Docker image is stored in a private Docker registry:

    ```bash
    --set wso2.deployment.imagePullSecrets=<IMAGE_PULL_SECRET>
    ```

When Micro Integrator is successfully deployed, it should create the following resources.

```bash
kubectl get deployments -n <NAMESPACE>
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
cloud-<RELEASE_NAME>     1/1     1            1           2m

kubectl get services -n <NAMESPACE>

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
cloud-<RELEASE_NAME>     ClusterIP   10.101.107.154   <none>        8253/TCP,9201/TCP,9164/TCP      2m

kubectl get ingress -n <NAMESPACE>
NAME                  HOSTS                      ADDRESS     PORTS     AGE
cloud-<RELEASE_NAME>  mi.wso2.com                10.0.2.15   80, 443   2m
```

!!! Tip
    The HOST of the ingress is the `hostname` given in the `values.yaml` file. The default host is `mi.wso2.com`.
    
You can list the pods deployed using the command:

```bash
kubectl get pods -n <NAMESPACE>
```

## Step 4 - Access the Micro Integrator deployment

To access the Micro Integrator deployment, follow these steps from your terminal:

1. Get the external IP (`EXTERNAL-IP`) of the Ingress resources by listing the Kubernetes ingresses.

    ```bash
    kubectl get ingress -n <NAMESPACE>
    ```

    Example:

    ```log
    NAME                   CLASS   HOSTS         ADDRESS        PORTS     AGE
    cloud-<RELEASE_NAME>   nginx   mi.wso2.com   <EXTERNAL-IP>   80, 443   27m
    ```

2. Add the above host information to your `/etc/hosts` file:

    ```bash
    <EXTERNAL-IP>   mi.wso2.com 
    ```

3. Execute the following command to invoke health check services:
    
    ```bash
    curl https://mi.wso2.com/healthz -k
    ```

## View integration process logs

Once you have deployed your integrations in the Kubernetes cluster, see the output of the running integration solutions using the pod's logs. 

1. First, you need to get the associated **pod id**. Use the `kubectl get pods` command to list down all the deployed pods.

    ```bash
    kubectl get pods -n <NAMESPACE>

    NAME                                        READY   STATUS    RESTARTS   AGE
    cloud-mi4-4-5b5c9b9c49-5hr44                1/1     Running   0          14h
    ingress-nginx-controller-5486b65c4d-57dkc   1/1     Running   2          2d
    ```

2.  To view the logs of the associated pod, run the `kubectl logs <pod name>` command. This will print the output of the given pod ID.

    ```bash
    kubectl logs cloud-mi4-4-5b5c9b9c49-5hr44 -n <NAMESPACE>

    [2025-03-06 10:19:15,026]  INFO {org.wso2.config.mapper.ConfigParser} - Overriding files in configuration directory /home/wso2carbon/wso2mi-4.4.0
    [2025-03-06 10:19:15,202]  INFO {org.wso2.config.mapper.ConfigParser} - Applying configurations with deployment configurations
    [2025-03-06 10:19:21,811] [] : mi :  INFO {org.apache.synapse.transport.passthru.core.PassThroughListeningIOReactorManager} - Pass-through HTTP Listener started on 0.0.0.0:8290
    [2025-03-06 10:19:21,813] [] : mi :  INFO {org.apache.synapse.transport.passthru.core.PassThroughListeningIOReactorManager} - Pass-through HTTPS Listener started on 0.0.0.0:8253
    [2025-03-06 10:19:22,006] [] : mi :  INFO {org.apache.synapse.transport.passthru.core.PassThroughListeningIOReactorManager} - Pass-through EI_INTERNAL_HTTP_INBOUND_ENDPOINT Listener started on 0.0.0.0:9201
    [2025-03-06 10:19:22,054] [] : mi :  INFO {org.apache.synapse.transport.passthru.core.PassThroughListeningIOReactorManager} - Pass-through EI_INTERNAL_HTTPS_INBOUND_ENDPOINT Listener started on 0.0.0.0:9164
    [2025-03-06 10:19:22,055] [] : mi :  INFO {org.wso2.micro.integrator.initializer.StartupFinalizer} - WSO2 Micro Integrator started in 7.04 seconds
    ...
    ```

## Deploy the Micro Integrator with an integration

To deploy the Micro Integrator with an integration (For example, `HelloDocker` sample), follow the instructions below. This section assumes you have already built a custom Docker image containing your integration, as outlined in the [HelloDocker sample guide]({{base_path}}/learn/samples/docker-sample/) and the [guide for generating Docker images]({{base_path}}/develop/generate-docker-image/).

### Step 1 - Build and push your custom Docker image

1. Build the Docker image containing your integration (For example, `HelloDocker`) as per the [HelloDocker sample guide]({{base_path}}/learn/samples/docker-sample/).
   
2. Once built, tag and push the image to a Docker registry or make it available locally for use in your Kubernetes cluster.

    - If you are using a local registry, no need to push the image to a remote repository.

### Step 2 - Update the `values.yaml` file for deployment

1. Open the `values.yaml` or `values_local.yaml` file in the `<HELM_HOME>/mi` directory.

2. Update the image repository and tag to point to the custom image you built (in this case, `hellodocker` as the repository and `1.0.0` as the tag). If you are using a local image, remove the `containerRegistry` entry.

    ```yaml
    # -- Container registry (leave this empty for local image)
    containerRegistry: ""

    wso2:
      deployment:
        image:
          repository: "hellodocker"  # Use your custom image repository
          tag: "1.0.0"  # Use the tag of your custom image
    ```

3. If you are using a private Docker registry, you will also need to add the `imagePullSecrets` parameter to your `values.yaml` file to authenticate against the registry. Set `imagePullSecrets` to the name of the Kubernetes secret that stores your Docker registry credentials.

    ```yaml
    wso2:
      deployment:
        imagePullSecrets: "<your_image_pull_secret>"  # Specify your image pull secret here
    ```

4. Save the changes to the `values.yaml` file. 

This configuration ensures that Kubernetes pulls the custom Docker image from your private registry using the specified credentials.

### Step 3 - Deploy the Micro Integrator with the custom integration

For a local deployment, use the `values_local.yaml` file, which is specifically configured for local setups without cloud-specific configurations.

1. Navigate to the `<HELM_HOME>/mi` directory in your terminal.
2. Run the Helm command using the `values_local.yaml` file for local deployment. Use the appropriate command based on your Helm version:

    - For **Helm v3**:

      ```bash
      helm install <RELEASE_NAME> ./ -f values_local.yaml -n <NAMESPACE> --create-namespace
      ```

    Make sure to specify the correct Kubernetes namespace (`<NAMESPACE>`) for the deployment.

3. Test the custom integration by running the following curl command:

    ```bash
    curl https://mi.wso2.com/HelloWorld -k
    ```

    You should receive the following response:

    ```json
    {"Hello": "World"}
    ```

### Invoke without Ingress controller

Once you have deployed your integrations in the Kubernetes cluster, you can also invoke the integration solutions without going through the Ingress controller by using the [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/#forward-a-local-port-to-a-port-on-the-pod) method for services. 

Follow the steps given below:

1. Apply port forwarding:

    ```bash
    kubectl port-forward service/<SERVICE_NAME> -n <NAMESPACE> 8290:8290
    ```

2. Invoke the proxy service:

    ```bash
    curl http://localhost:8290/HellowWorld -k
    ```

    You will receive the following response:

    ```bash
    {"Hello":"World"}
    ```

## Update the existing deployment 

You can update the configurations in the `values.yaml` file or the Docker image used by the deployment and upgrade your existing deployment.

```bash
helm upgrade <RELEASE_NAME> ./ -f values_local.yaml -n <NAMESPACE>
```

!!! Note
    The `pullPolicy` parameter in the `values.yaml` file should be set appropriately if you expect an updated Docker image to be pulled from the image resigstry.