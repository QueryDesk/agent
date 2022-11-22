# QueryDesk Agent

The agent is meant to be run inside private networks to allow access to databases that are not exposed to the public internet.

## Installation Instructions

Currently we only provide instructions for running in k8s. Let us know what other solutions you need.

1. Create your agent and download the config here: https://app.querydesk.com/agents. 

    Your config should look something like this:
    ```json
    {
      "agent_id": "agt_01GH0HABH34YWYV1E66YXXM6HG",
      "token": "xxxxxx"
    }
    ```
1. Get k8s setup to install the helm chart

    ```bash
    # make sure to reference the correct path to where you download the config.json file
    kubectl create namespace querydesk
    kubectl create secret generic agent-config --from-file config.json --namespace querydesk
    ```

    or to update the secret

    ```bash
    kubectl create secret generic agent-config --from-file config.json --namespace querydesk --dry-run=client -o yaml | kubectl apply -f -
    ```

1. Install app with helm

    ```bash
    # make sure to use helm 3.8.0 or later, or set `export HELM_EXPERIMENTAL_OCI=1`
    helm install agent oci://ghcr.io/querydesk/helm/agent --version 1.0.1 \
      --set querydesk.configExistingSecret=agent-config \
      --namespace querydesk
    ```