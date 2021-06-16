pipeline {

agent any

  stages {

    stage('Build') {
      environment {
        DOCKERHUB_CREDS = credentials('DH')
      }
      steps {
        
          // Build new image
          sh "docker build -t kiyange26773/jf3:${env.GIT_COMMIT} ."
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push kiyange26773/jf3:${env.GIT_COMMIT}"
        
      }
    }
    //deploy staging
    stage('Deploy Staging') {
      environment {
        GIT_CREDS = credentials('GIT')
      }
      steps {
        
          sh "git clone https://github.com/vatoscripts/argocd-demo-deploy.git"
          sh "git config --global user.email 'vatoscripts@gmail.com'"
          sh "git config --global user.name 'vatoscripts'"
         

          dir("argocd-demo-deploy") {
            sh "cd ./e2e && ls && sed 's/musesi/moses/g' names.txt > new_names.txt"
            //sh "git remote add origin https://github.com/vatoscripts/argocd-demo-deploy.git && git add ."
            sh "git remote set-url origin git@github.com:vatoscripts/argocd-demo-deploy.git"
            sh "git add ."
            sh "git status"
            sh "git commit -m 'publish new version' && git push"
          }
        
      }
    }

    //deploy to PROD
    stage('Deploy to Prod') {
      steps {
        input message:'Really Deploy?'
        
          dir("argocd-demo-deploy") {
            sh "cd ./prod && ls && kustomize edit set image kiyange26773/jf1:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version...' && git push || echo 'no changes made...'"
          }
    
      }
    }


    }
  }
