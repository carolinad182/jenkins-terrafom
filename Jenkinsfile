pipeline {
    agent any
    tools {
        "org.jenkinsci.plugins.terraform.TerraformInstallation" "terraform"
    }

    environment {
        TF_HOME = tool('terraform')
        TF_INPUT = "0"
        TF_IN_AUTOMATION = "TRUE"
        TF_LOG = "WARN"
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        PATH = "$TF_HOME:$PATH"
    }

    stages {
        stage('checkout') {
            steps {
                 
                          git url: 'https://github.com/g0t4/jgsu-spring-petclinic.git', branch: 'main'
                        
            }
        }
        stage('ApplicationInit'){
            steps {
                
                    sh 'terraform --version'
                    sh "terraform init"
                
            }
        }
        stage('ApplicationValidate'){
            steps {
                
                    sh 'terraform validate'
                
            }
        }
        stage('ApplicationPlan'){
            steps {
                
                    script {
                       
                        sh "terraform plan -out terraform-applications.tfplan;echo \$? > status"
                        stash name: "terraform-applications-plan", includes: "terraform-applications.tfplan"
                    }
                
            }
        }
        stage('ApplicationApply'){
            steps {
                script{
                    def apply = false
                    try {
                        input message: 'confirm apply', ok: 'Apply Config'
                        apply = true
                    } catch (err) {
                        apply = false
                        {
                            sh "terraform destroy -auto-approve"
                        }
                        currentBuild.result = 'UNSTABLE'
                    }
                    if(apply){
                        
                            unstash "terraform-applications-plan"
                            sh 'terraform apply terraform-applications.tfplan'
                        
                    }
                }
            }
        }
    }
}
