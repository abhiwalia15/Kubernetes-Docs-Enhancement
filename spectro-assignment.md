# Debug Kubernetes with Kubectl

Kubernetes offers a command line tool named [`kubectl`](https://kubernetes.io/docs/reference/kubectl/) that allows you to run debugging commands and communicate with a Kubernetes cluster's [`control plane`](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane). This page explains how to troubleshoot errors in Kubernetes using `kubectl`.

At the end of this guide, you will learn to identify, diagnose, and resolve issues with all your existing containers.

> **Tip:** You can use kubectl-tool for [stateless](https://kubernetes.io/docs/tutorials/stateless-application/) and [stateful](https://kubernetes.io/docs/tutorials/stateful-application/) applications in Kubernetes.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Debug Steps](#debug-steps)
2. [What's Next](#whats-next)

<div id='id-prerequisites'/>

## Prerequisites

- You have `kubectl` installed on your local machine. For installation instructions, follow the [official Kubernetes documentation](https://kubernetes.io/docs/tasks/tools/). 

- You have [deployed a sample application](https://docs.spectrocloud.com/kubernetes-knowlege-hub/tutorials/deploy-stateless-frontend-app/) and connected the `kubectl` Command-Line Tool (CLI) to the Kubernetes cluster.

<div id='debug-steps'/>

## Debug Steps

1. The first step is to get a list of available pods in your cluster. You can issue the [`kubectl get pods`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) command to list all available pods and display their status.

    * Issue the above command with `--namespace` or `-n`  flag to list pods in a specific namespace in Kubernetes. For example:

        ```shell
        # Replace <namespace> with the name of your namespace  
        kubectl get pods --namespace <namespace>
        ```

    * You can use `kubectl get pods --all-namespaces` to list all pods across all namespaces in a Kubernetes cluster.

2. You can retrieve more information about each pod using [`kubectl describe pod`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe). For example:

    ```shell
    # Replace <pod-name> with the name of your pod 
    kubectl describe pod <pod-name> -n <namespace>
    ```

    * The above command returns configuration information about the container(s) and pod (e.g., labels, resource requirements), as well as status information about the container(s) and pod (e.g., state, readiness, restart count, events).

    * You can now identify the pod(s) associated with the application you want to debug.

3. Issue the [`kubectl log`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) command to view the logs of a container that you want to debug.

    > :warning: You must specify the container's name if there are multiple containers running in a pod; otherwise, the command will fail. However, the container name is optional if the pod has only one container.

    ```shell
    # Use the argument -c to specify the container name
    kubectl logs <pod-name> -c <container-name>

    # This returns a snapshot of the logs for all containers
    kubectl logs <pod-name> --all-containers=true

    # Use the -p flag to display the logs of a pod that was previously running
    kubectl logs -p <pod-name>
    ```

    * Inspect the logs of the affected containers for error messages or any helpful information related to the issue.

4. Issue the [`kubectl exec`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec) command to further explore the container's environment, configuration files, and log files from within the pod. 

    * To access a `shell` inside a specific container and to connect it to your terminal, use the `-it` flag to activate an interactive terminal session. 

        ```shell
        kubectl exec -it <pod-name> -c <container-name> -- /bin/sh
        ```

        > **Tip:** You can specify any available `shell` by changing `/bin/sh` to the shell name you want, like `/bin/bash` or `/bin/zsh`. Make sure the specified shell is installed and available within the container.

5. (Optional) When troubleshooting complex issues, use the [`kubectl debug`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#debug) command for in-depth debugging. This command adds [ephemeral container](https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/) to a pod. For example: 

    ```shell
    # Inject an ephemeral container to troubleshoot your specific pod container
    kubectl debug -it <pod-name> --image=busybox
    ```

<div id='whats-next'/>

## What's Next 

* Refer to the Kubernetes documentation to learn more about debugging your [application](https://kubernetes.io/docs/tasks/debug/debug-application/) or [cluster](https://kubernetes.io/docs/tasks/debug/debug-cluster/).

* Read the [reference guide](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-) for essential `kubectl` commands.