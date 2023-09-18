template = '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: packer
  name: packer
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: hashicorp/packer
    name: packer
    '''

def buildNumber = env.BUILD_NUMBER

if (env.BRANCH_NAME == "main") {
    region = "us-east-1"
    key_pair = "my-laptop"
}

else if (env.BRANCH_NAME == "qa") {
    region = "us-east-2"
    key_pair = "my-laptop"
}

else if (env.BRANCH_NAME == "dev") {
    region = "us-west-1"
    key_pair = "my-laptop"
}

else {
    region = "us-west-2"
    key_pair = "my-laptop"
}


podTemplate(cloud: 'kubernetes', label: 'packer', yaml: template) {
    node("packer") {
        container("packer") {
            withCredentials([usernamePassword(credentialsId: 'AWS-credentials', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            withEnv(["AWS_REGION=${region}"]) {

            stage("Checkout SCM") {
                git branch: 'main', url: 'https://github.com/Beishenov7/packer-jenkins-april.git'
            }
            
            stage("Packer build") {
                sh "packer build -var jenkins_build_number=${buildNumber} packer.pkr.hcl"

                build job: 'hello-world', 
                parameters: [
                    string(name: 'action', value: 'apply'), 
                    string(name: 'region', value: "${region}"), 
                    string(name: 'ami_id', value: "my-ami-${buildNumber}"), 
                    string(name: 'az', value: "${region}b"), 
                    string(name: 'key_name', value: "${key_pair}")
                    ]
            }
        }
    }
}
}
}
