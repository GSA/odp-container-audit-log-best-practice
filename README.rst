

SSH to Jumphost
------------------------------------------------------------------------------

.. code-block:: bash

    EC2_PEM="~/ec2-pem/ociso-sanhe-dev.pem"
    EC2_USER="ec2-user"
    EC2_IP="52.91.237.19"
    echo EC2_PEM="${EC2_PEM}", EC2_USER="${EC2_USER}", EC2_IP="${EC2_IP}" && ssh -i ~/ec2-pem/${EC2_PEM}.pem ${EC2_USER}@${EC2_IP}


Install and Set Up kubectl
------------------------------------------------------------------------------

Reference:

- Install and Set Up kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/

.. code-block:: bash

    # Download the latest release with the command:
    curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"

    # Make the kubectl binary executable.
    chmod +x ./kubectl

    # Move the binary in to your PATH.
    sudo mv ./kubectl /usr/local/bin/kubectl

    #Test to ensure the version you installed is up-to-date:
    kubectl version --client


Configure Authentication to AWS EKS
------------------------------------------------------------------------------

Reference:

- Create a kubeconfig for Amazon EKS: https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html

.. code-block:: bash

    # verify your aws caller identity, make sure the iam instance profile of your jump box ec2
    # has sufficient privilege
    aws sts get-caller-identity

    # Use the AWS CLI update-kubeconfig command to create or update your kubeconfig for your cluster.
    # this command actually create an ~/.kube/config file
    EKS_CLUSTER_NAME="odp-eks-nbmAxSnm"
    aws eks --region us-east-1 update-kubeconfig --name ${EKS_CLUSTER_NAME}

    # Test your configuration.
    kubectl get svc


Attach Policy File to EKS Work Node
------------------------------------------------------------------------------

.. code-block:: bash

    ROLE_NAME="odp-eks-nbmAxSnm20201217151203708200000009"
    aws iam put-role-policy \
        --role-name ${ROLE_NAME} \
        --policy-name FluentBit-DaemonSet \
        --policy-document file://eks-fluent-bit-daemonset-policy.json


Create a Service Account
------------------------------------------------------------------------------

.. code-block:: bash

    kubectl create sa fluent-bit


Deploy Fluent-bit DaemonSet
------------------------------------------------------------------------------

.. code-block:: bash

    kubectl apply -f eks-fluent-bit-daemonset-rbac.yaml
    kubectl apply -f eks-fluent-bit-configmap.yaml
    kubectl apply -f eks-fluent-bit-daemonset.yaml
    kubectl apply -f eks-nginx-app.yaml


Reference:

- Centralized Container Logging with Fluent Bit: https://aws.amazon.com/blogs/opensource/centralized-container-logging-fluent-bit/