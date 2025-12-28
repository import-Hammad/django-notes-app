@Library("Shared") _  

pipeline {
    agent {label "vinod"}
    
    stages {
        stage("hello") {
            steps {
                script {
                    hello()
                }
            }
        }
        
        stage("code") {
            steps {
                script {
                    clone("https://github.com/import-Hammad/django-notes-app.git", "main")
                }
            }
        }  // ← This closing brace was missing
        
        stage("Build") {
            steps {
                sh "whoami"
                
                // Fix database settings to use SQLite
                sh '''
                    cd notesapp
                    sed -i "s/'ENGINE': 'django.db.backends.mysql'/'ENGINE': 'django.db.backends.sqlite3'/g" settings.py
                    sed -i "/ENGINE.*sqlite3/!b; n; s/'NAME'.*/'NAME': BASE_DIR \\/ 'db.sqlite3',/" settings.py
                    sed -i "/'USER'/d" settings.py
                    sed -i "/'PASSWORD'/d" settings.py
                    sed -i "/'HOST'/d" settings.py
                    sed -i "/'PORT'/d" settings.py
                    cat settings.py | grep -A 10 DATABASES
                '''
                
                script {
                    docker_build("notes-app", "latest", "piratehammad")
                }
            }
        }
        
        stage("Push to dockerhub") {
            steps {
                script{
                    docker_push("notes-app","latest","piratehammad")
                
                
                }
            }
        }
        
        stage("deploy") {
            steps {
                echo "this is deploying the code"
                sh "docker-compose down || true"
                sh "docker-compose up -d"
                    
            }
        }
    }
}
