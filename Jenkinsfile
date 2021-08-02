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
        stage ('STASH'){
            steps{
                stash name: 'gol-war-file', includes: '**/gameoflife.war'
            }
        }
        stage ('UNSTASH'){
            agent {label 'TOMCAT'}
            steps{
                unstash name: 'gol-war-file'
            }
        }
    }
    post{
        success{
            junit '**/TEST-*.xml'
            archive '**/*.war'
            echo 'The build has been successful'
            mail subject: 'Build is successful', to: 'devs@jenkinsbuild.com', from: 'admins@jenkinsbuild.com', body: 'BUILD_ID '+env.BUILD_URL 

        }
        failure{
            echo 'The build failed, please check'
            mail subject: 'Build failed!', to: 'devs@jenkinsbuild.com', from: 'admins@jenkinsbuild.com', body: 'BUILD_ID '+env.BUILD_URL
        }
    }
}
