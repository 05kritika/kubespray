pipeline {   
  agent {
    node {
      label 'master'
    }  
  }
  stages {
    
	stage('Checkout Code') {
      steps {
        checkout scm
      }
    }
    
	stage('Terraform Infra Plan') {
     steps {
       sh '''
	       cd contrib/terraform/aws/ 
           terraform init
		   terraform plan -var-file=credentials.tfvars
	      '''
      }
    }
    
    stage('Approval') {
      options {
        timeout(time: 1, unit: 'HOURS') 
      }
      steps {
        input 'approve the plan to proceed and apply'
      }
    }
    
	stage('Terraform Infra Provisioning') {
      steps {
	    sh '''
	        cd contrib/terraform/aws/
	        terraform destroy -auto-approve -var-file=credentials.tfvars    
	       '''
	  // terraform destroy -auto-approve -var-file=credentials.tfvars	
          
//        cleanWs()
         }
    }
	  
//    stage ("Waiting_prior_starting_ansible_provisioning") {
//      steps {
//         echo 'Waiting 2 minutes for deployment to complete prior starting smoke testing'
//         sleep 120 // seconds
//      }
//    }	    
	
    stage('Ansible Kubernetes Cluster Provisioning') {
      steps {
        sh '''
             ansible-playbook -i ./inventory/hosts ./cluster.yml -e ansible_user=ubuntu -b --become-user=root --flush-cache -e cloud_provider=aws -e ansible_ssh_private_key_file=/var/lib/jenkins/workspace/kubespray-pipeline/kritika-key.pem
	   '''
      }
    }
  }
}
