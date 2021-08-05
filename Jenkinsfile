pipeline{
    agent{
        label 'GOL'
    }
    parameters{
        string (name: 'BRANCH', defaultValue: 'master', description: 'choose which branch has to be executed')
        choice(name: 'GOALS', choices: ['package', 'clean package', 'install'], description: 'choose the appropriate MAVEN goal')
    }
    stages{
        stage ('SCM'){
            steps{
                mail subject: 'Build Started!', to: 'devs@jenkinsbuild.com', from: 'admins@jenkinsbuild.com', body: 'BUILD_ID '+env.BUILD_URL
                branch "${params.BRANCH}"
                git 'https://github.com/rvkrthk/game-of-life.git'
            }
        }
        stage ('BUILD'){
            steps{
                sh "mvn ${params.GOALS}"
            }
        }
        stage ('Server'){
            steps {
               rtServer (
                    id: "JFROG",
                    url: 'http://34.134.44.185:8081/artifactory',
                    username: 'admin',
                    password: 'Maxima100%',
                    /*bypassProxy: true,*/
                    timeout: 300
                )
            }
        }
        stage('Upload'){
            steps{
                rtUpload (
                serverId:"JFROG" ,
                    spec: '''{
                        "files": [
                            {
                            "pattern": "*.war",
                            "target": "gameoflife"
                            }
                        ]
                    }''',
                )
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "JFROG"
                )
            }
        }
    }
    post{
        success{
            junit '**/TEST-*.xml'
            archiveArtifacts '**/*.war'
            echo 'The build has been successful'
            mail subject: 'Build is successful', to: 'devs@jenkinsbuild.com', from: 'admins@jenkinsbuild.com', body: 'BUILD_ID '+env.BUILD_URL 

        }
        failure{
            echo 'The build failed, please check'
            mail subject: 'Build failed!', to: 'devs@jenkinsbuild.com', from: 'admins@jenkinsbuild.com', body: 'BUILD_ID '+env.BUILD_URL
        }
    }
}