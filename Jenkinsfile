pipeline {
    agent any
    parameters {
        choice name: 'ACTION', choices: ['DEPLOY', 'DESTROY']
        string name: 'CHART_NAME', defaultValue: 'simple-web'
        string name: 'NAMESPACE', defaultValue: 'itay'
    }
    environment {
        KUBECONFIG = "${WORKSPACE}/.kubeconfig"
    }
    stages {
        stage('Setup') {
            steps {
                sh "az login -i"
                sh "az aks get-credentials -n devops-interview-aks -g devops-interview-rg --overwrite-existing"
                sh "kubelogin convert-kubeconfig -l msi"
                sh "kubectl config set-context --current --namespace=$NAMESPACE"
            }
        }
        stage('Deploy') {
            when {
                expression {
                    return params.ACTION == 'DEPLOY';
                }
            }
            steps {
               sh "helm upgrade --install $CHART_NAME helm/$CHART_NAME/ --values helm/$CHART_NAME/values.yaml"
            }
        }
        stage('Destroy') {
            when {
                expression {
                    return params.ACTION == 'DESTROY';
                }
            }
            steps {
                sh "helm uninstall $CHART_NAME"
            }
        }
    }
}

