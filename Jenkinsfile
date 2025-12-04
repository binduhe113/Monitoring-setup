pipeline {
    agent any

    environment {
        PROMETHEUS_CONFIG_DIR = "${WORKSPACE}/prometheus"
    }

    stages {

        stage('Checkout Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/binduhe113/Monitoring-setup.git'
            }
        }

        stage('Run Node Exporter') {
            steps {
                sh '''
                # Stop & Remove existing Node Exporter
                docker ps -q --filter "name=node-exporter" | grep -q . && docker stop node-exporter && docker rm node-exporter || true

                # Start Node Exporter
                docker run -d \
                  --name=node-exporter \
                  -p 9100:9100 \
                  prom/node-exporter
                '''
            }
        }

        stage('Run Prometheus') {
            steps {
                sh '''
                mkdir -p $PROMETHEUS_CONFIG_DIR
                cp prometheus.yml $PROMETHEUS_CONFIG_DIR/prometheus.yml

                # Stop & Remove old Prometheus
                docker ps -q --filter "name=prometheus" | grep -q . && docker stop prometheus && docker rm prometheus || true

                # Run Prometheus
                docker run -d \
                  --name=prometheus \
                  -p 9090:9090 \
                  -v $PROMETHEUS_CONFIG_DIR/prometheus.yml:/etc/prometheus/prometheus.yml \
                  prom/prometheus
                '''
            }
        }

        stage('Run Grafana') {
            steps {
                sh '''
                # Stop & Remove old Grafana
                docker ps -q --filter "name=grafana" | grep -q . && docker stop grafana && docker rm grafana || true

                # Run Grafana
                docker run -d \
                  --name=grafana \
                  -p 3000:3000 \
                  grafana/grafana
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Monitoring stack deployed successfully!"
            echo
