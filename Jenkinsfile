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

properties([
    parameters([
        choice(choices: ['dev', 'qa', 'stage', 'prod'], description: 'Pick environment', name: 'region')
        ])
        ])

if (params.region = "dev") {
    region = "us-east-1"
}

else if (params.region = "qa") {
    region = "us-east-2"
}

else if (params.region = "stage") {
    region = "us-west-1"
}

else if (params.region = "prod") {
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
