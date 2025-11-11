# MinIO Tenant GitOps Repository: Sealed Secrets Guide
This guide outlines the process for encrypting sensitive MinIO credentials using the Bitnami Sealed Secrets controller, ensuring that the secret data remains encrypted within your Git repository.PrerequisitesThe Sealed Secrets Controller must be installed and running in your Kubernetes cluster (kube-system namespace).You must have the kubeseal CLI tool installed locally.Encryption WorkflowThe encryption process requires two steps: fetching the cluster's public certificate and then using that certificate to seal the raw Kubernetes Secret.
## Step 1: Fetch the Public Encryption Certificate
Run this command once to download the public certificate from the running Sealed Secrets controller. This certificate is used to encrypt the Secret so that only the controller in your cluster can decrypt it.
`kubeseal --fetch-cert > cluster-public-cert.pem`

## Step 2: Seal the MinIO Root Secret
This command reads your raw (unencrypted) Secret YAML file, uses the downloaded public certificate for encryption, and outputs the final SealedSecret resource.Ensure your raw secret file (minio-root-secret-raw.yaml) exists and contains the base64-encoded credentials before running this command.

`kubeseal --cert cluster-public-cert.pem --scope cluster-wide < ./minio-config/dev-storage/minio-root-secret-raw.yaml --format=yaml > ./minio-config/dev-storage/minio-root-secret.yaml`

The resulting file, minio-root-secret.yaml, is safe to commit to your Git repository.
