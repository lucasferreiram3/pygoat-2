pipeline {
    agent any 
    environment {
        caminhoPacote = 'uploadToVeracode/pygoat.zip'
        wrapperVersion = '23.8.12.0'
    }

    stages {
        stage('Clean') { 
            steps {
                sh 'rm -rf pipeline-scan-LATEST.zip pipeline-scan.jar'
                sh 'rm -rf veracode-wrapper.jar'
                sh 'rm -rf uploadToVeracode'
            }
        }

        stage('Archive') { 
            steps {
                sh 'mkdir uploadToVeracode'
                sh 'zip -r -v uploadToVeracode/pygoat.zip . -i "*.py" "*.js" "*.html" '
            }
        }

        stage('Veracode SAST - Pipeline Scan') { 
            steps {
                withCredentials([usernamePassword(credentialsId: 'veracode-credentials', passwordVariable: 'VKEY', usernameVariable: 'VID')]) {
                sh 'curl -sSO https://downloads.veracode.com/securityscan/pipeline-scan-LATEST.zip'
                sh 'unzip -o pipeline-scan-LATEST.zip'

                sh ("""java -jar pipeline-scan.jar \
                    --veracode_api_id "${VID}" \
                    --veracode_api_key "${VKEY}" \
                    --request_policy "Veracode Recommended POV" 
                    """)
            
                sh (""" java -jar pipeline-scan.jar \
                    --veracode_api_id "${VID}" \
                    --veracode_api_key "${VKEY}" \
                    --file ${caminhoPacote} \
                    --project_name "Pygoat-Showroom" \
                    --policy_file "Veracode_Recommended_POV.json"
                    """)
                }
            }
        }
    }
}