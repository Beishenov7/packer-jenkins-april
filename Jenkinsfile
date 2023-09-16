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


if (env.BRANCH_NAME == "main" ) {
    region = "us-east-1"
}

else if (env.BRANCH_NAME == "qa") {
    region = "us-east-2"
}

else if (env.BRANCH_NAME == "dev") {
    region = "us-west-1"
}

else {
    region = "us-west-2"
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
            }
        }
    }
}
    }
}
