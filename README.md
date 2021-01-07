This is a model for creating in Python an EKS environment with the AWS provider on Pulumi using GitHub Actions.

- Access "https://github.com/smashse/pulumi-iac-eks"
- Click in "Use this template"
- Create a new repository from template "pulumi-iac-eks"(example "pulumi-iac-eks") and chose as "Private"

# Install AWS (Optional)

```bash
cd /tmp
ssh-keygen -f pulumi_eks_py_access
curl "<https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip>" -o "awscliv2.zip"
unzip awscliv2.zip && sudo ./aws/install
aws configure
aws ec2 import-key-pair --public-key-material "$(cat pulumi_eks_py_access.pub | base64)" --key-name pulumi_eks_py_access --region us-west-2 --profile yourprofile
```

## Download the PULUMI template

```bash
mkdir -p $HOME/Pulumi
cd $HOME/Pulumi
git clone https://github.com/yourgithubuser/pulumi-iac-eks.git
cd pulumi-iac-eks
```

## Install Pulumi on Linux by running the installation script:

```bash
curl -fsSL https://get.pulumi.com | sh && bash
```

## Install Python VirtualEnv:

```bash
sudo apt -y install python3-virtualenv
```

## Create a "pulumi_eks_py" project:

```bash
cd $HOME/Pulumi/pulumi-iac-eks/pulumi_eks_py
```

**Note:** If you want to change the name given to Kubernetes cluster, execute the command below in the template folder.

```bash
sed -i "s/"template-"/"desiredname-"/g" *.py
```

## Install Python Requirements

```bash
python3 -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip setuptools wheel
python -m pip install -r requirements.txt
```

## Perform an initial deployment, run the following commands:

```bash
pulumi login
pulumi stack init pulumi_eks_py
```

## Set AWS_PROFILE:

```bash
pulumi config set aws:profile yourprofile
```

## Set AWS_REGION:

```bash
pulumi config set aws:region us-west-2
```

## Review the "pulumi_eks_py" project

```bash
pulumi preview
```

## Enable Workflow

```bash
cd $HOME/Pulumi/pulumi-iac-eks/.github/workflows
```

```bash
mv pull_request.yml.template pull_request.yml
mv push.yml.template push.yml
```

## Environment Variables

There are a number of Environment Variables that can be set to interact with the action:

- By default, Pulumi will try to connect to the [Pulumi SaaS](https://app.pulumi.com/). For this to happen, the GitHub Action needs to be passed a "PULUMI_ACCESS_TOKEN".

## Amazon Web Services (AWS)

For AWS, you'll need to create or use or use an existing IAM user for your action. Please see [the Pulumi documentation page](https://pulumi.io/quickstart/aws/setup.html#environment-variables) for pointers to the relevant AWS documentation for doing this.

As soon as you have an AWS user in hand, you'll set the environment variables "AWS_ACCESS_KEY_ID" and "AWS_SECRET_ACCESS_KEY" using GitHub Secrets, and then consume them in your action.

**Note:** Go to Settings> Secrets and add "PULUMI_ACCESS_TOKEN","AWS_ACCESS_KEY_ID" and "AWS_SECRET_ACCESS_KEY" as new repository secret.

## Commit the changes

```bash
cd $HOME/Pulumi/pulumi-iac-eks/
```

```bash
git add *
git add .github/workflows/*
git add .pulumi/*
git add pulumi_eks_py/*
git commit -m "pulumi-iac-eks"
git push
```

## Access EKS Kubernetes cluster

```bash
sudo snap install kubectl --classic
aws eks list-clusters --region us-west-2 --profile yourprofile
aws eks --region us-west-2 --profile yourprofile update-kubeconfig --name $(pulumi stack output cluster-name)
kubectl get po --all-namespaces
```

## Destroy the "pulumi_eks_py" project

```bash
cd $HOME/Pulumi/pulumi-iac-eks/pulumi_eks_py
pulumi destroy
```

## Remove the "pulumi_eks_py" project from Stack

```bash
cd $HOME/Pulumi/pulumi-iac-eks/pulumi_eks_py
pulumi stack rm pulumi_eks_py
```

## Source:

<https://www.pulumi.com/docs/get-started/>

<https://www.pulumi.com/docs/reference/pkg/>

<https://www.pulumi.com/docs/intro/concepts/state/>

<https://www.pulumi.com/docs/guides/continuous-delivery/github-actions/>

<https://github.com/pulumi/actions>
