---
title: "Google Cloud"
description: "How to use Codefresh with Google Cloud"
group: integrations
redirect_from:
  - /docs/deploy-your-containers/kubernetes/
toc: true
---

Codefresh has native support for Google Cloud in the following areas:

- [Connecting to Google registries]({{site.baseurl}}/docs/docker-registries/external-docker-registries/google-container-registry/)
- [Deploying to GKE]({{site.baseurl}}/docs/deploy-to-kubernetes/add-kubernetes-cluster/#adding-gke-cluster)
- [Using Google Storage for Test reports]({{site.baseurl}}/docs/testing/test-reports/#connecting-a-google-bucket)
- [Using Google Storage for Helm charts]({{site.baseurl}}/docs/new-helm/add-helm-repository/#private-repository---gcs)
- [Using Cloud Build]({{site.baseurl}}/docs/integrations/gcloud-builder/)
- [Installing the runner via the marketplace]({{site.baseurl}}/docs/integrations/google-marketplace/)


## Using Google Container Registries

Google Container registries are fully compliant with the Docker registry API that Codefresh follows. You can connect GCR like any [other Docker registry]({{site.baseurl}}/docs/docker-registries/external-docker-registries/google-container-registry/).

{% 
	include image.html 
	lightbox="true" 
file="/images/integrations/docker-registries/add-gcr-registry.png" 
url="/images/integrations/docker-registries/add-gcr-registry.png" 
alt="Connecting to GCR" 
caption="Connecting to GCR" 
max-width="70%" 
%}

Once the registry is added you can the [standard push step]({{site.baseurl}}/docs/codefresh-yaml/steps/push/) step in pipelines. See also the documentation page for [working with Docker registries]({{site.baseurl}}/docs/docker-registries/working-with-docker-registries/).

## Deploying to Google Kubernetes Engine

Codefresh has native support for connecting a GKE cluster in the [cluster configuration screen]({{site.baseurl}}/docs/deploy-to-kubernetes/add-kubernetes-cluster/).

{% 
	include image.html 
	lightbox="true" 
file="/images/integrations/google-cloud/gke-integration.png" 
url="/images/integrations/google-cloud/gke-integration.png" 
alt="Connecting a GKE cluster" 
caption="Connecting a GKE cluster" 
max-width="40%" 
%}

Once the cluster is connected you can use any of the [available deployment options]({{site.baseurl}}/docs/deploy-to-kubernetes/deployment-options-to-kubernetes/) for Kubernetes clusters. You also get access to all other Kubernetes dashboards such as the [cluster dashboard]({{site.baseurl}}/docs/deploy-to-kubernetes/manage-kubernetes/)  or the [environment dashboard]({{site.baseurl}}/docs/deploy-to-kubernetes/environment-dashboard/) .

## Storing test reports in Google Cloud storage

Codefresh has native support for test reports. You can store the reports on Google Cloud storage.

{% include
image.html
lightbox="true"
file="/images/integrations/google-cloud/google-cloud-storage.png"
url="/images/integrations/google-cloud/google-cloud-storage.png"
alt="Google cloud storage"
caption="Google cloud storage"
max-width="80%"
%}

See the full documentation for [test reports]({{site.baseurl}}/docs/testing/test-reports/).

## Using Google Storage for storing Helm charts

You can connect Google storage as a Helm repository in the [integrations screen]({{site.baseurl}}/docs/new-helm/add-helm-repository/).

{% include
image.html
lightbox="true"
file="/images/integrations/google-cloud/google-storage-helm-repo.png"
url="/images/integrations/google-cloud/google-storage-helm-repo.png"
alt="Using Google Cloud for Helm charts"
caption="Using Google Cloud for Helm charts"
max-width="60%"
%}

Once you connect your Helm repository you can use it any [Codefresh pipeline with the Helm step]({{site.baseurl}}/docs/new-helm/using-helm-in-codefresh-pipeline/). 

## Using Google Cloud build

Codefresh has a [native Docker build step]({{site.baseurl}}/docs/codefresh-yaml/steps/build/) for creating Docker images. As an alternative method of building Docker images you can also use [Google Cloud Build]({{site.baseurl}}/docs/integrations/gcloud-builder/) in a Codefresh pipeline.

## Installing the Codefresh runner from the Google Marketplace

The [Codefresh runner]({{site.baseurl}}/docs/administration/codefresh-runner/) is a Kubernetes native application that allows you to run pipelines on your own Kubernetes cluster (even behind the firewall). Specifically for Google Cloud, the runner is also available via the [marketplace]({{site.baseurl}}/docs/integrations/google-marketplace/).


## Traditional Google Cloud deployments

For any other Google Cloud deployment you can use the [Google Cloud CLI from a Docker image](https://hub.docker.com/r/google/cloud-sdk/) in a [freestyle step]({{site.baseurl}}/docs/codefresh-yaml/steps/freestyle/)

`YAML`
{% highlight yaml %}
{% raw %}
  create_a_vm:
    title: "Creating a Virtual machine"
    type: "freestyle"
    arguments:
      image: "google/cloud-sdk:slim"
      commands:
        - echo $KEY_FILE | base64 --decode > key_file.json
        - gcloud compute instances create demo-codefresh --image codefresh-simple-ubuntu-vm --zone europe-west1-b --metadata-from-file startup-script=startup.sh --tags http-server --preemptible --quiet   
{% endraw %}
{% endhighlight %}

See the example of [uploading to a Google Bucket]({{site.baseurl}}/docs/yaml-examples/examples/uploading-or-downloading-from-gs/) or [creating a VM]({{site.baseurl}}/docs/yaml-examples/examples/packer-gcloud/) for more details.


 


## What to read next

- [Add your cluster]({{site.baseurl}}/docs/deploy-to-kubernetes/add-kubernetes-cluster/)
- [Manage your Kubernetes cluster]({{site.baseurl}}/docs/deploy-to-kubernetes/manage-kubernetes/)
- [Cloning Git repositories]({{site.baseurl}}/docs/yaml-examples/examples/git-checkout/)
