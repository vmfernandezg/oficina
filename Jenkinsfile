pipeline {
    agent any
 
    stages {

	      stage ('Checkout') {
            steps {
              checkout scm
            }
	      }

	      stage('Create Image') {
            steps {
              sh '/usr/local/bin/packer validate packer.json'
              sh '/usr/local/bin/packer build -force packer.json'
            }
        }
	  
        stage('TF Plan') {
            steps {
                sh '/usr/local/bin/terraform init -input=false'
                sh '/bin/rm -fR /var/lib/jenkins/workspace/Oficina/terraform.tfstate'
                /* sh '/usr/local/bin/terraform init -backend-config="storage_account_name=vmfernandezg" -backend-config="container_name=terraform" -backend-config="access_key=f4QfzMXGv9+snDAjy77yULFIEXYvwntNCG5CAOFb8yyG+K7rdDhl2SFxErMh9VdAgChkm1t9fqpAuUfVi5MXlw==" -backend-config="key=terraform.tfstate"' */
                sh '/usr/local/bin/terraform plan -out=myplan -input=false'
       	    }
     	  }

	      stage('Validate') {
      	    steps {
                sh '/usr/local/bin/terraform validate'
	          }
	      }
    	  
	      stage('Approval-Apply') {
            steps {
              script {
              def userInput = input(id: 'confirm', message: 'Apply Terraform?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply terraform', name: 'confirm'] ])
            }
                sh '/usr/local/bin/terraform apply -input=false myplan'
            }
        }
        
        stage('Approval-Configuration') {
            steps {
              script {
              def userInput = input(id: 'confirm', message: 'Apply Terraform?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply terraform', name: 'confirm'] ])
            }
              sh '/usr/bin/whoami'
              sh ':> /var/lib/jenkins/.ssh/known_hosts'
              sh '''
                /usr/bin/ansible-playbook -i inventory.yml  apache-ansible.yml -u arqsis --ssh-extra-args="'-o StrictHostKeyChecking=no'"
              '''
              /* ansiblePlaybook (
                credentialsId: 'arqsis',
                hostKeyChecking: false,
                inventory: 'inventory.yml',
                playbook: 'apache-ansible.yml'
              ) */
            } 
        }
    }    
}