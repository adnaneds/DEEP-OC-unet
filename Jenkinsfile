#!/usr/bin/groovy

@Library(['github.com/indigo-dc/jenkins-pipeline-library@1.4.0']) _

pipeline {
    agent {
        label 'docker-build'
    }

    environment {
        dockerhub_repo = "deephdc/uc-adnaneds-deep-oc-unet"
        base_cpu_tag = "2.9.1"
        base_gpu_tag = "2.9.1-gpu"
    }

    stages {
        stage('Validate metadata') {
            steps {
                checkout scm
                sh 'deep-app-schema-validator metadata.json'
            }
        }
        stage('Docker image building') {
            when {
                anyOf {
                    branch 'main'
                    branch 'test'
                    buildingTag()
                }
            }
            steps{

                dir('deep-oc-user_app'){
                    checkout scm
                    script {
                        // build different tags
                        id = "${env.dockerhub_repo}"

                        if (env.BRANCH_NAME == 'main') {
                           // CPU (aka latest, i.e. default)
                           id_cpu = DockerBuild(id,
                                            tag: ['latest', 'cpu'],
                                            build_args: ["tag=${env.base_cpu_tag}",
                                                         "branch=main"])

                           // GPU
                           id_gpu = DockerBuild(id,
                                            tag: ['gpu'],
                                            build_args: ["tag=${env.base_gpu_tag}",
                                                         "branch=main"])
                        }

                        if (env.BRANCH_NAME == 'test') {
                           // CPU
                           id_cpu = DockerBuild(id,
                                            tag: ['test', 'cpu-test'],
                                            build_args: ["tag=${env.base_cpu_tag}",
                                                         "branch=test"])

                           // GPU
                           id_gpu = DockerBuild(id,
                                            tag: ['gpu-test'],
                                            build_args: ["tag=${env.base_gpu_tag}",
                                                         "branch=test"])
                        }
                    }
                }
            }
            post {
                failure {
                    DockerClean()
                }
            }
        }

        stage('Docker Hub delivery') {
            when {
                anyOf {
                   branch 'main'
                   branch 'test'
                   buildingTag()
               }
            }
            steps{
                script {
                    DockerPush(id_cpu)
                    DockerPush(id_gpu)
                }
            }
            post {
                failure {
                    DockerClean()
                }
                always {
                    cleanWs()
                }
            }
        }

        stage("Render metadata on the marketplace") {
            when {
                allOf {
                    branch 'main'
                    changeset 'metadata.json'
                }
            }
            steps {
                script {
                    def job_result = JenkinsBuildJob("Pipeline-as-code/deephdc.github.io/pelican")
                    job_result_url = job_result.absoluteUrl
                }
            }
        }
    }
}
