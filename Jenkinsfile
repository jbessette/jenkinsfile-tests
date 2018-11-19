boolean isRelease = false
String cronString = ""

if ("$env.TAG_NAME" != "null") {
    isRelease = true
    cronString = "" // Don't put release builds on a schedule. Jenkins will kick off one build when a new tag is first discovered.
} else {
    cronString = "H/5 * * * *"
}

def ConditionalClean(boolean clean) {
    if (clean) {
        cleanWs()
    }
}

pipeline {
    agent any
    triggers { 
        cron(cronString)
    }
    options {
        skipDefaultCheckout true
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '1')
    }
    stages {
        stage('Test') {
            when { equals expected: true, actual: isRelease }
            steps {
                powershell "Write-Host 'This is a release: $env.TAG_NAME'"
            }
        }
        stage('Checkout repositories') { 
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: scm.branches,
                    extensions: scm.extensions + [
                        [$class: 'CleanBeforeCheckout'], 
                        [$class: 'CloneOption', depth: 0, noTags: false, reference: '', shallow: false, timeout: 60],
                        [$class: 'RelativeTargetDirectory', relativeTargetDir: 'subdir1']
                    ],
                    userRemoteConfigs: scm.userRemoteConfigs
                ])

            }
        }
       stage('All Build')
       {
           steps {
                dir('C:\\') {
                    bat "mklink \\D c:\\tb \"${env.WORKSPACE}\""
                }               
               powershell 'Write-Host ' + currentBuild.getBuildCauses()
               powershell 'Write-Host "Build: ' + BRANCH_NAME + '"'
           }
       }
       stage('Tag Build')
       {
           when {tag '*'}
           steps {
               powershell 'Write-Host "Tag release: ' + BRANCH_NAME + '"'
           }
       }
    }
    post {
        success {
            powershell 'Write-Host ' + BRANCH_NAME
        }
        cleanup {
            ConditionalClean(isRelease)
        }
    }

}