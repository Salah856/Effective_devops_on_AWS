# AWS CloudFormation designer

AWS CloudFormation designer is a tool that lets you create and edit CloudFormation templates using a graphic user interface. 

Designer hides a lot of the complexity of editing a CloudFormation template using a standard text editor. You can access it directly at https://console.aws.amazon.com/cloudformation/designer or in the CloudFormation dashboard after you click on Create Stack:

![image](https://user-images.githubusercontent.com/23625821/130321274-82fb3804-ef94-4358-88b2-85f4f36b7fcb.png)

The workflow is fairly simple. You simply drag and drop resources from the left-hand side menu into a canvas. Once your resources are added, you can then connect them to other resources using the small dots surrounding each resource icon. In the preceding example, we are connecting an EC2 instance to its security group. There are a number of hidden gems that can help you when designing your template.

