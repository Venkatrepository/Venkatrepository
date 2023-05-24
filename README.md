import boto3

def lambda_handler(event, context):
   client = boto3.client('ec2')
   client1 = boto3.client('sns')

   message = ""
   regions = [region['RegionName'] for region in client.describe_regions()['Regions']]


   for i in regions:
      ec2_cli=boto3.client("ec2",region_name=i)
      response = ec2_cli.describe_images(Owners=['self'])
      if len(response['Images']) > 0:
         #print('regionname :',i)
         message += 'regionname :' + i + ' \n'
         for ami in response['Images']:
           if  ami['Public']:
              message += 'Image IDs:' + ami['ImageId']
         print(message)

   message = "The Following List of  public AMI. Request you to initiate appropirate action. \n " + message
   response1 = client1.publish(
       TargetArn=<put the ARN of SNS>,
       Message=message,
       Subject='List of  public AMI' 
      )  
