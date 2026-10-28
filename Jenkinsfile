pipeline {
    agent any

    triggers {
        // Jenkins cron syntax: check once every five minutes, with a spread start time.
        cron('H/5 * * * *')
    }

    options {
        disableConcurrentBuilds()
        timestamps()
    }

    parameters {
        string(
            name: 'GITHUB_HTML_URL',
            defaultValue: '',
            description: 'Full URL of the GitHub HTML page to monitor'
        )
    }

    stages {
        stage('Check GitHub page') {
            steps {
                script {
                    if (!params.GITHUB_HTML_URL?.trim()) {
                        error('Set GITHUB_HTML_URL in this Jenkins job to the GitHub page to monitor.')
                    }
                }

                sh '''#!/bin/sh
                    set -eu

                    page_file="github-page.html"
                    hash_file=".github-page.sha256"

                    curl --fail --location --silent --show-error \\
                        --retry 3 --retry-delay 2 \\
                        "$GITHUB_HTML_URL" --output "$page_file"

                    current_hash=$(sha256sum "$page_file" | awk '{print $1}')
                    previous_hash=""
                    if [ -f "$hash_file" ]; then
                        previous_hash=$(cat "$hash_file")
                    fi

                    if [ -z "$previous_hash" ]; then
                        echo "Baseline saved for $GITHUB_HTML_URL (SHA-256: $current_hash)."
                        echo "BASELINE" > check-result.txt
                    elif [ "$current_hash" = "$previous_hash" ]; then
                        echo "No page changes detected (SHA-256: $current_hash)."
                        echo "UNCHANGED" > check-result.txt
                    else
                        echo "Page changed: $GITHUB_HTML_URL"
                        echo "Previous SHA-256: $previous_hash"
                        echo "Current SHA-256:  $current_hash"
                        echo "CHANGED" > check-result.txt
                    fi

                    printf '%s\\n' "$current_hash" > "$hash_file"
                '''

                archiveArtifacts artifacts: 'github-page.html,check-result.txt', fingerprint: true
            }
        }
    }
}

