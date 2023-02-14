pipeline {
    
    agent any
    stages {
        stage("Checkout") {
            steps {

               checkout scm
        }
            
            
    }
        
        stage("update git"){
        steps{
            withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'pass', usernameVariable: 'user')]) {
                  script {
                        env.encodedPass=URLEncoder.encode(pass, "UTF-8")
                    }
    

        sh 'git config user.email "${GITHUB_EMAIL}"'
        sh 'git config user.name "${GITHUB_USERNAME}"'
        sh "cat ./dev/deploy.yaml"
        echo "${DOCKERTAG}"
        sh "sed -i 's+image-registry.openshift-image-registry.svc:5000/dhanya-jenkins/node-gitserver.*+image-registry.openshift-image-registry.svc:5000/dhanya-jenkins/node-gitserver:${DOCKERTAG}+g' ./dev/deploy.yaml"
        sh "sed -i 's+image-registry.openshift-image-registry.svc:5000/dhanya-jenkins/node-gitclient.*+image-registry.openshift-image-registry.svc:5000/dhanya-jenkins/node-gitclient:${DOCKERTAG}+g' ./dev/deploy.yaml"       
        sh "cat ./dev/deploy.yaml"
        sh "git add ."
        sh "git commit -m 'done by jenkins job node-app-update' "
        sh  'git push https://$user:$encodedPass@github.com/$user/nodeapp-update.git HEAD:branch'
            }
        }
        }
        stage('deploy') {
        steps {
            script {
                openshift.withCluster() {
                    openshift.withProject("$PROJECT_NAME") {
                        echo "Using project: ${openshift.project()}"
                         sh 'oc project "$PROJECT_NAME" '
                         sh 'oc apply -f dev/deploy.yaml'
                    }
                }
            }
        } 
    }
        }
}
