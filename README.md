# QueryDesk Agent

The agent is meant to be run inside private networks to allow access to databases that are not exposed to the public internet. It also allows you to keep database credentials secure, when using the agent you don't need to expose your passwords or ssl certs outside of your network.

## Installation Instructions

Currently we only provide instructions for running in k8s. Let us know what other solutions you need.

1. Create your agent and download the config here: https://app.querydesk.io/agents. 

    Your config should look something like this:
    ```json
    {
      "agent_id": "674cd578-911e-4d1c-810c-db92b385a7ad",
      "credentials": [
        {
          "database_id": "",
          "description": "",
          "password": "",
          "reviews_required": 0,
          "username": ""
        }
      ],
      "token": "xxxxxx"
    }
    ```
1. You will then need to create your database configurations here: https://app.querydesk.io/databases. The list view provides the id needed for `database_id` in the provided config.
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
    helm install agent oci://ghcr.io/querydesk/helm/agent --version 1.0.0 \
      --set querydesk.configExistingSecret=agent-config \
      --namespace querydesk
    ```