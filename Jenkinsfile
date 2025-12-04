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
                echo "==> Removing old node-exporter if exists"
                docker rm -f node-exporter || true

                echo "==> Starting Node Exporter"
                docker run -d \
                  --name node-exporter \
                  -p 9100:9100 \
                  prom/node-exporter
                '''
            }
        }

        stage('Run Prometheus') {
            steps {
                sh '''
                echo "==> Preparing Prometheus directory"
                mkdir -p $PROMETHEUS_CONFIG_DIR
                cp prometheus.yml $PROMETHEUS_CONFIG_DIR/prometheus.yml

                echo "==> Removing old Prometheus container"
                docker rm -f prometheus || true

                echo "==> Starting Prometheus"
                docker run -d \
                  --name prometheus \
                  -p 9090:9090 \
                  -v $PROMETHEUS_CONFIG_DIR/prometheus.yml:/etc/prometheus/prometheus.yml \
                  prom/prometheus
                '''
            }
        }

        stage('Run Grafana') {
            steps {
                sh '''
                echo "==> Removing old Grafana container"
                docker rm -f grafana || true

                echo "==> Starting Grafana"
                docker run -d \
                  --name grafana \
                  -p 3000:3000 \
                  grafana/grafana
                '''
            }
        }
    }
    post {
        success {
            echo "✅ Monitoring stack deployed successfully!"
            echo "Grafana → http://<EC2_PUBLIC_IP>:3000"
            echo "Prometheus → http://<EC2_PUBLIC_IP>:9090"
            echo "Node Exporter → http://<EC2_PUBLIC_IP>:9100/metrics"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins logs."
        }
    }
}



   
