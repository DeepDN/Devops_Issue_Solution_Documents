# branch selection before build with dynamially fetch

```yaml
pipeline {
    agent any

    stages {
        stage('Select Branch') {
            steps {
                script {
                    def gitBranches = sh(
                        script: 'git ls-remote --heads https://github.com/Sheetalsgs/nodeapp.git',
                        returnStdout: true
                    ).trim().readLines()
                    def branches = gitBranches.collect { it.split()[1].replaceAll("refs/heads/","") }
                    
                    def userInput = input(
                        message: 'Please select a branch to build:',
                        parameters: [
                            [$class: 'ChoiceParameterDefinition', 
                             choices: branches.join('\n'), 
                             description: 'Select branch to build', 
                             name: 'BRANCH']
                        ]
                    )
                    env.BRANCH = userInput
                }
            }
        }

        stage('Build') {
            steps {
                git branch: "${env.BRANCH}", url: 'https://github.com/Sheetalsgs/nodeapp.git'
                sh 'npm install'
                sh 'npm run build'
            }
        }
    }
}
```