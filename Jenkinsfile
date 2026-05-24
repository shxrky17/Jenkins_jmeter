pipeline {

    agent any

    tools {
        jdk 'jdk17'
    }

    environment {
        JMETER_HOME = 'E:\\apache-jmeter-5.6.3'
    }

    stages {

        stage('Checkout Code') {

            steps {
                git 'https://github.com/shxrky17/Jenkins_jmeter.git'
            }
        }

        stage('Run JMeter Test') {

            steps {

                bat """
                %JMETER_HOME%\\bin\\jmeter.bat ^
                -n ^
                -t tests\\sprintttjmx.jmx ^
                -l results.csv ^
                -e ^
                -o report
                """
            }
        }

        stage('Performance Gate') {

            steps {

                script {

                    def lines = readFile('results.csv').split('\n')

                    int total = lines.size() - 1
                    int failed = 0

                    for (line in lines.drop(1)) {

                        def cols = line.split(',')

                        if (cols[7] == 'false') {
                            failed++
                        }
                    }

                    double errorRate = (failed * 100.0) / total

                    echo "Total Requests: ${total}"
                    echo "Failed Requests: ${failed}"
                    echo "Error Rate: ${errorRate}%"

                    if (errorRate > 2.0) {

                        error("Performance Gate Failed: Error rate exceeded 2%")
                    }
                }
            }
        }

        stage('Publish HTML Report') {

            steps {

                publishHTML(target: [
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }
    }
}