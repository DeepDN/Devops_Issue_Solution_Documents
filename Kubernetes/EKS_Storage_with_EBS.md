# EKS Storage with EBS - Elastic Block Store
<aside>
 **Step-02:  Create IAM policyy**

</aside>

- Go to Services -> IAM
- Create a Policy
    - Select JSON tab and copy paste the below JSON
    
    ```yaml
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "ec2:AttachVolume",
            "ec2:CreateSnapshot",
            "ec2:CreateTags",
            "ec2:CreateVolume",
            "ec2:DeleteSnapshot",
            "ec2:DeleteTags",
            "ec2:DeleteVolume",
            "ec2:DescribeInstances",
            "ec2:DescribeSnapshots",
            "ec2:DescribeTags",
            "ec2:DescribeVolumes",
            "ec2:DetachVolume"
          ],
          "Resource": "*"
        }
      ]
    }
    ```
    
    - Review the same in **Visual Editor**
    - Click on **Review Policy**
    - **Name:** Amazon_EBS_CSI_Driver
    - **Description:** Policy for EC2 Instances to access Elastic Block Store
    - Click on **Create Policy**
    
    <aside>
     **Get the IAM role Worker Nodes using and Associate this policy to that role**
    
    </aside>
    
    ```yaml
    # Get Worker node IAM Role ARN
    kubectl -n kube-system describe configmap aws-auth
    
    # from output check rolearn
    rolearn: arn:aws:iam::180789647333:role/eksctl-eksdemo1-nodegroup-eksdemo-NodeInstanceRole-IJN07ZKXAWNN
    ```
    
    <aside>
    
    
    - Go to Services -> IAM -> Roles
    - Search for role with name **eksctl-eksdemo1-nodegroup** and open it
    - Click on **Permissions** tab
    - Click on **Attach Policies**
    - Search for **Amazon_EBS_CSI_Driver** and click on **Attach Policy**
    
    </aside>
    
    <aside>
     **Step-04: Deploy Amazon EBS CSI Driver**
    
    </aside>
    
    - Verify kubectl version, it should be 1.14 or later
    
    ```yaml
    kubectl version --client --short
    ```
    
    - Deploy Amazon EBS CSI Driver
    
    ```yaml
    # Deploy EBS CSI Driver
    kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
    
    # Verify ebs-csi pods running
    kubectl get pods -n kube-system
    ```