#!groovy

pipeline {
    agent any
    options {
        timestamps()
        timeout(time: 2, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '5'))
        disableConcurrentBuilds()
    }
    environment {
        // In case another branch beside master or develop should be deployed, enter it here
        BRANCH_TO_DEPLOY = 'qt5.11'
        // This version will be used for the image tags if the branch is merged to master
        BUILDER_IMAGE_VERSION = '1.4'
        BUILDER_IMAGE_DEVELOP_VERSION = 'qt5.11'
        DISCORD_WEBHOOK = credentials('991ce248-5da9-4068-9aea-8a6c2c388a19')
    }
    stages {
        stage('Notification') {
            steps {
                // Using result state 'ABORTED' to mark the message on discord with a white border.
                // Makes it easier to distinguish job-start from job-finished
                discordSend(
                        description: "Started build #$env.BUILD_NUMBER",
                        image: '',
                        link: "$env.BUILD_URL",
                        successful: true,
                        result: "ABORTED",
                        thumbnail: 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png',
                        title: "$env.JOB_NAME",
                        webhookURL: "${DISCORD_WEBHOOK}"
                )
            }
        }
        stage('Build image') {
            when {
                not {
                    anyOf { branch 'develop'; branch 'master'; branch "${BRANCH_TO_DEPLOY}" }
                }
            }
            //noinspection GroovyAssignabilityCheck
            parallel {
                stage('Debian') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Debian/Dockerfile --rm -t spectreproject/spectre-builder-debian:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                /*
                stage('CentOS') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f CentOS/Dockerfile --rm -t spectreproject/spectre-builder-centos:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Fedora') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Fedora/Dockerfile --rm -t spectreproject/spectre-builder-fedora:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Raspberry Pi') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f RaspberryPi/Dockerfile --rm -t spectreproject/spectre-builder-raspi:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Ubuntu') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Ubuntu/Dockerfile --rm -t spectreproject/spectre-builder-ubuntu:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                */
            }
        }
        stage('Build and upload image') {
            when {
                anyOf { branch 'develop'; branch "${BRANCH_TO_DEPLOY}" }
            }
            //noinspection GroovyAssignabilityCheck
            parallel {
                stage('Debian') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Debian/Dockerfile --rm -t spectreproject/spectre-builder-debian:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-debian:${BUILDER_IMAGE_DEVELOP_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                /*
                stage('CentOS') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f CentOS/Dockerfile --rm -t spectreproject/spectre-builder-centos:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-centos:${BUILDER_IMAGE_DEVELOP_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Fedora') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Fedora/Dockerfile --rm -t spectreproject/spectre-builder-fedora:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-fedora:${BUILDER_IMAGE_DEVELOP_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Raspberry Pi') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f RaspberryPi/Dockerfile --rm -t spectreproject/spectre-builder-raspi:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-raspi:${BUILDER_IMAGE_DEVELOP_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Ubuntu') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Ubuntu/Dockerfile --rm -t spectreproject/spectre-builder-ubuntu:${BUILDER_IMAGE_DEVELOP_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-ubuntu:${BUILDER_IMAGE_DEVELOP_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                */
            }
        }
        stage('Release and upload image') {
            when {
                branch 'master'
            }
            //noinspection GroovyAssignabilityCheck
            parallel {
                stage('Debian') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Debian/Dockerfile --rm -t spectreproject/spectre-builder-debian:${BUILDER_IMAGE_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-debian:${BUILDER_IMAGE_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('CentOS') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f CentOS/Dockerfile --rm -t spectreproject/spectre-builder-centos:${BUILDER_IMAGE_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-centos:${BUILDER_IMAGE_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Fedora') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Fedora/Dockerfile --rm -t spectreproject/spectre-builder-fedora:${BUILDER_IMAGE_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-fedora:${BUILDER_IMAGE_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Raspberry Pi') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f RaspberryPi/Dockerfile --rm -t spectreproject/spectre-builder-raspi:${BUILDER_IMAGE_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-raspi:${BUILDER_IMAGE_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
                stage('Ubuntu') {
                    agent {
                        label "docker"
                    }
                    steps {
                        script {
                            withDockerRegistry(credentialsId: '051efa8c-aebd-40f7-9cfd-0053c413266e') {
                                sh "docker build -f Ubuntu/Dockerfile --rm -t spectreproject/spectre-builder-ubuntu:${BUILDER_IMAGE_VERSION} ."
                                sh "docker push spectreproject/spectre-builder-ubuntu:${BUILDER_IMAGE_VERSION}"
                            }
                        }
                    }
                    post {
                        always {
                            sh "docker system prune --all --force"
                        }
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                if (!hudson.model.Result.SUCCESS.equals(currentBuild.getPreviousBuild()?.getResult())) {
                    emailext(
                            subject: "GREEN: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                            body: '${JELLY_SCRIPT,template="html"}',
                            recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
//                            to: "to@be.defined",
//                            replyTo: "to@be.defined"
                    )
                }
                discordSend(
                        description: "Build #$env.BUILD_NUMBER finished successfully",
                        image: '',
                        link: "$env.BUILD_URL",
                        successful: true,
                        thumbnail: 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png',
                        title: "$env.JOB_NAME",
                        webhookURL: "${DISCORD_WEBHOOK}"
                )
            }
        }
        unstable {
            emailext(
                    subject: "YELLOW: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                    body: '${JELLY_SCRIPT,template="html"}',
                    recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
//                    to: "to@be.defined",
//                    replyTo: "to@be.defined"
            )
            discordSend(
                    description: "Build #$env.BUILD_NUMBER finished unstable",
                    image: '',
                    link: "$env.BUILD_URL",
                    successful: true,
                    result: "UNSTABLE",
                    thumbnail: 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png',
                    title: "$env.JOB_NAME",
                    webhookURL: "${DISCORD_WEBHOOK}"
            )
        }
        failure {
            emailext(
                    subject: "RED: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                    body: '${JELLY_SCRIPT,template="html"}',
                    recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
//                    to: "to@be.defined",
//                    replyTo: "to@be.defined"
            )
            discordSend(
                    description: "Build #$env.BUILD_NUMBER failed!",
                    image: '',
                    link: "$env.BUILD_URL",
                    successful: false,
                    thumbnail: 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png',
                    title: "$env.JOB_NAME",
                    webhookURL: "${DISCORD_WEBHOOK}"
            )
        }
    }
}
