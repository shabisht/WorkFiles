# Transfer single file to AWS server from local system.

- step 1 : go to your local terminal
- step 2 : type scp -i <path to .pem file> <path to local file to be uploaded> <ec2-user@public DNS url>:<folder path where to be copied in server>
- Exampe -  scp -i /home/Downloads/abc.pem /home/Desktop/Test_Project/code/test1.py ubuntu@ec2-xyz*.compute.amazonaws.com/home/ubuntu/Project/code/

# Transfer whole folder to AWS server from local system.
- just add -r before local folder url
- Example -  scp -i /home/Downloads/abc.pem -r /home/Desktop/Test_Project ubuntu@ec2-xyz*.compute.amazonaws.com/home/ubuntu/Project/

# Visit official docs on [aws](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html)
