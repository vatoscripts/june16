pipeline {

agent any

  stages {

    stage('Build') {
      environment {
        DOCKERHUB_CREDS = credentials('DH')
      }
      steps {
        
          // Build new image
          sh "docker build -t kiyange26773/jf7:${env.GIT_COMMIT} ."
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push kiyange26773/jf7:${env.GIT_COMMIT}"
        
      }
    }
    //deploy staging
    stage('Deploy Staging') {
      environment {
        GIT_CREDS = credentials('GIT')
      }
      steps {
         
          sh "git clean -fd"
          sh "mkdir cddirectory && cd cddirectory"
          sh "git clone https://github.com/vatoscripts/argocddemodeploy11.git"
          sh "git config --global user.email 'vatoscripts@gmail.com'"
          sh "git config --global user.name 'vatoscripts'"
         
         
          dir("cddirectory/argocddemodeploy11") {
            sh "cd ./e2e && ls && sed 's/musesi/moses/g' names.txt > new_names.txt"
            sh "git remote add origin https://github.com/vatoscripts/argocddemodeploy1.git"
            //sh "git remote set-url origin git@github.com:vatoscripts/argocddemodeploy.git"
            sh "git add ."
            sh "git status"
            sh "git commit -m 'publish new version.....' && git push"
          }
        
      }
    }

    //deploy to PROD
    stage('Deploy to Prod') {
      steps {
        input message:'Really Deploy?'
        
          dir("cddirectory/argocddemodeploy11") {
           // sh "cd ./prod && ls && kustomize edit set image kiyange26773/jf1:${env.GIT_COMMIT}"
           // sh "git commit -am 'Publish new version...' && git push || echo 'no changes made...'"
          }
    
      }
    }


    }
  }
