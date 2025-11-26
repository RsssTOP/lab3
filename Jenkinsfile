pipeline {
    agent { label 'l1' }  // твой нод RomanS

    environment {
        STACK_NAME   = 'lab3-stack'
        FLAVOR       = 'm1.small'
        IMAGE        = 'ununtu-22.04'
        NETWORK_NAME = 'students-net'
        SEC_GROUP    = 'students-general'
        KEY_NAME     = 'RomanS1'
        SERVER_NAME  = 'Lab3'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/RsssTOP/lab3.git'
            }
        }

        stage('Deploy via Heat') {
            steps {
                sh '''
                    set -ex
                    . /home/ubuntu/openrc-jenkins.sh

                    if openstack stack list -f value -c "Stack Name" | grep -w "$STACK_NAME"; then
                      echo "Stack $STACK_NAME уже существует — обновляем"
                      openstack stack update \
                        --parameter FLAVOR=$FLAVOR \
                        --parameter IMAGE=$IMAGE \
                        --parameter NETWORK_NAME=$NETWORK_NAME \
                        --parameter SEC_GROUP=$SEC_GROUP \
                        --parameter KEY_NAME=$KEY_NAME \
                        --parameter SERVER_NAME=$SERVER_NAME \
                        --wait \
                        "$STACK_NAME" \
                        server.yaml || echo "update вернулся с ошибкой"
                    else
                      echo "Stack $STACK_NAME не найден — создаём"
                      openstack stack create \
                        --parameter FLAVOR=$FLAVOR \
                        --parameter IMAGE=$IMAGE \
                        --parameter NETWORK_NAME=$NETWORK_NAME \
                        --parameter SEC_GROUP=$SEC_GROUP \
                        --parameter KEY_NAME=$KEY_NAME \
                        --parameter SERVER_NAME=$SERVER_NAME \
                        --wait \
                        --template server.yaml \
                        "$STACK_NAME" || echo "create вернулся с ошибкой"
                    fi
                '''
            }
        }
    }
}
