# Jenkins GitHub HTML page monitor

This Pipeline downloads a GitHub HTML page every five minutes and compares its SHA-256 hash with the previous check. A changed page is reported in the Jenkins console and the downloaded HTML and check result are archived as build artifacts.

## Configure Jenkins

1. Create a Pipeline job and point it at this repository (or paste in this `Jenkinsfile`).
2. Set the `GITHUB_HTML_URL` parameter to the full page URL, such as `https://github.com/OWNER/REPOSITORY`.
3. Run the job once to save the initial baseline. The Pipeline trigger then schedules checks every five minutes.

The Jenkins agent needs `curl`, `sha256sum`, and `awk`, and a persistent workspace for this job so the saved hash survives between builds. The job disables concurrent builds to avoid overlapping checks. GitHub pages that require authentication need separate credential handling; this example is for publicly accessible HTML pages.
