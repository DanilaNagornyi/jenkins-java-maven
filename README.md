# Jenkins Java Maven CI/CD Pipeline

<div align="center">

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Groovy](https://img.shields.io/badge/Groovy-4298B8?style=for-the-badge&logo=apache-groovy&logoColor=white)

</div>

## 📋 About

Jenkins CI/CD pipeline project demonstrating Multibranch Pipeline with modular Groovy scripts. The project showcases separation of pipeline logic, automated Maven builds, Docker image creation, and push to DockerHub registry.

## 🎯 Key Features

- 🔄 **Multibranch Pipeline** - Automated builds for multiple branches
- 📦 **Modular Groovy Scripts** - Reusable pipeline logic
- 🚀 **Maven Build Automation** - Spring Boot JAR creation
- 🐳 **Docker Integration** - Automated image build and push
- 🔐 **Credentials Management** - Secure DockerHub authentication
- 📊 **Build Stages** - Clear separation of concerns

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────┐
│            Jenkins Pipeline Stages              │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. Init                                        │
│     └── Load script.groovy                     │
│                                                 │
│  2. Build JAR                                   │
│     └── mvn clean package                      │
│                                                 │
│  3. Build Image                                 │
│     ├── docker build                           │
│     ├── docker login                           │
│     └── docker push to DockerHub              │
│                                                 │
│  4. Deploy                                      │
│     └── Placeholder for deployment logic       │
│                                                 │
└─────────────────────────────────────────────────┘
```

## 🛠️ Technology Stack

### Backend
- **Java 8** - Programming language
- **Spring Boot 2.3.5** - Application framework
- **Maven 3.9** - Build automation tool
- **Amazon Corretto 8** - JRE runtime

### CI/CD
- **Jenkins** - Automation server
- **Groovy** - Pipeline scripting language
- **Declarative Pipeline** - Pipeline-as-Code

### DevOps
- **Docker** - Containerization platform
- **DockerHub** - Container registry
- **Alpine Linux** - Lightweight base image

## 📂 Project Structure

```
.
├── src/                        # Java source code
├── target/                     # Maven build output
├── Jenkinsfile                 # Pipeline definition
├── script.groovy              # Modular pipeline functions
├── Dockerfile                  # Docker image definition
├── freestyle-build.sh         # Freestyle project script
└── pom.xml                    # Maven configuration
```

## 🔄 Pipeline Stages

### Stage 1: Init
```groovy
Load external Groovy script for modular pipeline functions
- Loads script.groovy
- Makes functions available to pipeline
```

### Stage 2: Build JAR
```groovy
buildJar()
- Execute: mvn clean package
- Creates Spring Boot executable JAR
- Output: target/java-maven-app-*.jar
```

### Stage 3: Build Image
```groovy
buildImage()
- Build Docker image: artnagornyi/demo-app:jma-2.0
- Login to DockerHub using credentials
- Push image to registry
```

### Stage 4: Deploy
```groovy
deployApp()
- Placeholder for deployment logic
- Can be extended for Kubernetes/EC2/etc.
```

## 🚀 Quick Start

### Prerequisites

- Java 8+
- Maven 3.9+
- Docker
- Jenkins with plugins:
  - Pipeline
  - Git
  - Credentials
  - Docker Pipeline

### Jenkins Setup

#### 1. Create Multibranch Pipeline Project

```
New Item → Multibranch Pipeline
```

#### 2. Configure Branch Sources

- **Source:** Git
- **Repository URL:** https://github.com/DanilaNagornyi/jenkins-java-maven.git
- **Credentials:** (Add GitHub credentials if private)

#### 3. Add DockerHub Credentials

```
Jenkins → Credentials → Add Credentials
- Type: Username with password
- ID: docker-hub-repo
- Username: [Your DockerHub username]
- Password: [Your DockerHub password/token]
```

#### 4. Configure Maven Tool

```
Jenkins → Global Tool Configuration → Maven
- Name: maven-3.9
- Install automatically or specify MAVEN_HOME
```

### Local Build

```bash
# Clone repository
git clone https://github.com/DanilaNagornyi/jenkins-java-maven.git
cd jenkins-java-maven

# Build with Maven
mvn clean package

# Build Docker image
docker build -t demo-app .

# Run container
docker run -p 8080:8080 demo-app
```

Application will be available at `http://localhost:8080`

## 📝 Pipeline Configuration

### Jenkinsfile
```groovy
def gv

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    stages {
        stage('init') {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage('build jar') {
            steps {
                script {
                    gv.buildJar()
                }
            }
        }
        stage('build image') {
            steps {
                script {
                    gv.buildImage()
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    gv.deployApp()
                }
            }
        }
    }
}
```

### script.groovy
```groovy
def buildJar() {
    echo 'building the application...'
    sh 'mvn clean package'
}

def buildImage() {
    echo "building the docker image..."
    withCredentials([usernamePassword(
        credentialsId: 'docker-hub-repo',
        passwordVariable: 'PASS',
        usernameVariable: 'USER'
    )]) {
        sh "docker build -t artnagornyi/demo-app:jma-2.0 ."
        sh 'echo $PASS | docker login -u $USER --password-stdin'
        sh "docker push artnagornyi/demo-app:jma-2.0"
    }
}

def deployApp() {
    echo 'deploying the application...'
}

return this
```

## 🐳 Dockerfile

```dockerfile
FROM amazoncorretto:8-alpine3.17-jre

EXPOSE 8080

COPY ./target/java-maven-app-*.jar /usr/app/
WORKDIR /usr/app

CMD java -jar java-maven-app-*.jar
```

**Features:**
- Lightweight Alpine-based image
- Amazon Corretto JRE 8
- Exposes port 8080
- Runs Spring Boot application

## 🔐 Security Best Practices

- ✅ Credentials stored in Jenkins Credentials Manager
- ✅ Passwords never hardcoded in pipeline
- ✅ Docker login with stdin to avoid command history
- ✅ Separate Groovy script for better code organization

## 📊 Modular Pipeline Benefits

### Why External Groovy Script?

1. **Reusability** - Functions can be used across multiple pipelines
2. **Maintainability** - Easier to update logic in one place
3. **Readability** - Jenkinsfile stays clean and declarative
4. **Testing** - Groovy functions can be tested independently
5. **Separation of Concerns** - Pipeline structure vs. implementation

## 🔧 Extension Ideas

### Add Version Increment Stage
```groovy
stage('increment version') {
    steps {
        script {
            sh 'mvn build-helper:parse-version versions:set \
                -DnewVersion=\\${parsedVersion.majorVersion}.\\${parsedVersion.minorVersion}.\\${parsedVersion.nextIncrementalVersion} \
                versions:commit'
        }
    }
}
```

### Add Automated Tests
```groovy
stage('test') {
    steps {
        script {
            sh 'mvn test'
        }
    }
}
```

### Add Version Commit Stage
```groovy
stage('commit version') {
    steps {
        script {
            withCredentials([usernamePassword(
                credentialsId: 'github-credentials',
                passwordVariable: 'PASS',
                usernameVariable: 'USER'
            )]) {
                sh 'git config user.email "jenkins@example.com"'
                sh 'git config user.name "jenkins"'
                sh 'git add pom.xml'
                sh 'git commit -m "ci: version bump"'
                sh 'git push origin HEAD:main'
            }
        }
    }
}
```

## 🎯 Freestyle vs Pipeline

This project focuses on **Pipeline** approach:

| Freestyle | Pipeline |
|-----------|----------|
| UI-based configuration | Code-based (Pipeline as Code) |
| Limited version control | Full Git integration |
| Hard to share/replicate | Easy to version and share |
| Less flexible | Highly flexible with Groovy |

The `freestyle-build.sh` is kept for comparison purposes.

## 📈 Multibranch Pipeline Features

- 🌿 **Automatic branch detection** - Scans repository for branches
- 🔄 **Branch-specific builds** - Each branch has its own build history
- 🗑️ **Automatic cleanup** - Removes jobs for deleted branches
- 🔀 **PR/MR support** - Can build pull/merge requests
- 📋 **Shared configuration** - Same Jenkinsfile for all branches

## 🤝 Contributing

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is created for educational purposes.

## 👨‍💻 Author

**Danila Nagornyi**

- GitHub: [@DanilaNagornyi](https://github.com/DanilaNagornyi)

---

<div align="center">

⭐ Star this repo if you find it useful!

</div>
