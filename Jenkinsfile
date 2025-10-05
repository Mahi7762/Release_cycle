 stages {
        stage("Git Checkout") {
            steps {
                git branch: '25Q1', url: 'https://github.com/Mahi7762/Release_cycle.git'
            }
        }

        stage("Copy to HTTPD (Local)") {
            steps {
                sh '''
                    sudo cp /root/.jenkins/workspace/25Q1/index.html /var/www/html/
                    sudo chmod -R 777 /var/www/html/index.html
                '''
            }
        }

        stage('Build Ubuntu Docker Image with HTTPS') {
            steps {
                script {
                    writeFile file: 'Dockerfile', text: '''
                        FROM ubuntu:latest
                        RUN apt-get update && \
                            apt-get install -y apache2 openssl && \
                            mkdir -p /etc/apache2/ssl && \
                            openssl req -x509 -nodes -days 365 \
                              -subj "/C=IN/ST=Maharashtra/L=Pune/O=DevOps/CN=localhost" \
                              -newkey rsa:2048 \
                              -keyout /etc/apache2/ssl/apache.key \
                              -out /etc/apache2/ssl/apache.crt && \
                            a2enmod ssl && \
                            echo "<VirtualHost *:443>
                                SSLEngine on
                                SSLCertificateFile /etc/apache2/ssl/apache.crt
                                SSLCertificateKeyFile /etc/apache2/ssl/apache.key
                                DocumentRoot /var/www/html
                            </VirtualHost>" > /etc/apache2/sites-available/default-ssl.conf && \
                            a2ensite default-ssl && \
                            systemctl enable apache2
                        
                        COPY index.html /var/www/html/index.html
                        EXPOSE 443
                        CMD ["apachectl", "-D", "FOREGROUND"]
                    '''
                    sh '''
                        docker build -t ubuntu-https:latest .
                    '''
                }
            }
        }

        stage('Run Ubuntu Container with HTTPS') {
            steps {
                sh '''
                    docker rm -f my-ubuntu-https || true
                    docker run -d --name my-ubuntu-https -p 8443:443 ubuntu-https:latest
                '''
            }
        }

        stage("Access Info") {
            steps {
                echo "✅ Container running with HTTPS on port 8443"
                echo "👉 Access it using: https://<your-server-ip>:8443"
            }
        }
    }

