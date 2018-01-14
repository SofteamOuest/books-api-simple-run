#!groovy
import java.text.SimpleDateFormat

// pod utilisé pour la compilation du projet
podTemplate(label: 'meltingpoc-kubectl-pod', nodeSelector: 'medium', containers: [

        // le slave jenkins
        containerTemplate(name: 'jnlp', image: 'jenkinsci/jnlp-slave:alpine'),

        // un conteneur pour déployer les services kubernetes
        containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl', command: 'cat', ttyEnabled: true)],

        // montage nécessaire pour que le conteneur docker fonction (Docker In Docker)
        volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
) {

    node('meltingpoc-kubectl-pod') {

        properties([
            parameters([string(defaultValue: 'DEFAULT', description: 'version à déployer', name: 'image')]),
            gitLabConnection(''),
            buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '',
                daysToKeepStr: '1', numToKeepStr: '3')),
            pipelineTriggers([])])

         stage('checkout sources'){
                    checkout scm;
         }

        container('kubectl') {

            stage('deploy'){

                sh "sed s/version/$image/ deployment.yml > deployment-version.yml"
                sh 'kubectl apply -f deployment-version.yml || :'

            }
        }
    }
}
