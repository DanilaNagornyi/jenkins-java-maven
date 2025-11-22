def gv

pipeline {   
    agent any
    tools {
        maven 'maven-3.9'
    }
    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
		stage('increment version') {
		    steps {
		        script {
		            echo 'incrementing app version...'
		            sh 'mvn build-helper:parse-version versions:set \
		                -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
		                versions:commit'
		            def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
		            def version = matcher[0][1]
		            env.IMAGE_NAME = "$version-$BUILD_NUMBER"
		        }
		    }
		}
        stage("build jar") {
            steps {
                script {
                    gv.buildJar()

                }
            }
        }

        stage("build image") {
            steps {
                script {
                    gv.buildImage()
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    gv.deployApp()
                }
            }
        }

		stage('commit version update') {
			steps {
				script {
					withCredentials([string(credentialsId: 'GitHub-pat', variable: 'GITHUB_TOKEN')]) {

						sh '''
                    git config user.email "jenkins@example.com"
                    git config user.name "jenkins"
                '''

						sh '''
                    git status
                    git branch
                '''

						sh '''
                    git add .
                    git commit -m "ci: version bump" || true
                '''

						sh '''
                    git push --force https://x-access-token:${GITHUB_TOKEN}@github.com/DanilaNagornyi/jenkins-java-maven.git HEAD:jenkins-jobs
                '''
					}
				}
			}
		}

	}
} 
