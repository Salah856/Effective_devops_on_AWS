# AWS CloudFormation designer

AWS CloudFormation designer is a tool that lets you create and edit CloudFormation templates using a graphic user interface. 

Designer hides a lot of the complexity of editing a CloudFormation template using a standard text editor. You can access it directly at https://console.aws.amazon.com/cloudformation/designer or in the CloudFormation dashboard after you click on Create Stack:

![image](https://user-images.githubusercontent.com/23625821/130321274-82fb3804-ef94-4358-88b2-85f4f36b7fcb.png)

The workflow is fairly simple. You simply drag and drop resources from the left-hand side menu into a canvas. Once your resources are added, you can then connect them to other resources using the small dots surrounding each resource icon. In the preceding example, we are connecting an EC2 instance to its security group. There are a number of hidden gems that can help you when designing your template.

You can use right-click on resources and directly access the documentation for the CloudFormation resource, as follows:

![image](https://user-images.githubusercontent.com/23625821/130321499-c1fa3fd8-988a-43dd-9a98-ade50393cd33.png)

When dragging a dot to connect two resources together, designer will highlight resources compatible with that connection:

![image](https://user-images.githubusercontent.com/23625821/130321520-60f58b8d-02c4-4604-805a-fe86c025b10e.png)

The editor on the bottom section of the designer supports auto completion using Ctrl + space:

![image](https://user-images.githubusercontent.com/23625821/130321537-467a0be7-6142-49c0-b101-e705c6cb091c.png)

Once your template is complete, you can simply click on a button and go from designing your stack to launching it.

## Cloudformer

