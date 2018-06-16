# Jenkins

## Jenkinsfile

### Jenkins with docker

#### Use Docker as execution environment

```
pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B'
            }
        }
    }
}
```

Using multiple containers

```
pipeline {
    agent none
    stages {
        stage('Back-end') {
            agent {
                docker { image 'maven:3-alpine' }
            }
            steps {
                sh 'mvn --version'
            }
        }
        stage('Front-end') {
            agent {
                docker { image 'node:7-alpine' }
            }
            steps {
                sh 'node --version'
            }
        }
    }
}
```

Using a Dockerfile

```
pipeline {
    agent { dockerfile true }
    stages {
        stage('Test') {
            steps {
                sh 'node --version'
                sh 'svn --version'
            }
        }
    }
}
```

sidecar 

```
node {
    checkout scm
    docker.image('mysql:5').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw"') { c ->
        docker.image('mysql:5').inside("--link ${c.id}:db") {
            /* Wait until mysql service is up */
            sh 'while ! mysqladmin ping -hdb --silent; do sleep 1; done'
        }
        docker.image('centos:7').inside("--link ${c.id}:db") {
            /*
             * Run some tests which require MySQL, and assume that it is
             * available on the host name `db`
             */
            sh 'make check'
        }
    }
}
```

#### Building containers

```
node {
    checkout scm
    def customImage = docker.build("my-image:${env.BUILD_ID}")
    customImage.push()

    customImage.push('latest')
}
```

```
node {
    checkout scm
    def testImage = docker.build("test-image", "./dockerfiles/test") 

    testImage.inside {
        sh 'make test'
    }
}
```

### Jenkinsfile 

Jenkinsfile with multiple nodes

```
pipeline {
    stages {
        stage('Tox') {
            steps {
                node('os_linux') {
                    sh 'tox -v --recreate'
                }
                node('os_mac') {
                    sh 'tox -v --recreate'
                }
            }
        }

    }
}
```