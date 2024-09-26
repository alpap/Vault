# Nexus

 Sonatype Nexus is a popular repository manager used for managing and organizing software components. It acts as a central hub for storing, sharing, and retrieving software libraries, artifacts, and binaries used in development and deployment processes. Here’s a breakdown of the basics:

## Types of Nexus Repositories
- Proxy Repositories: These act as a caching proxy for remote repositories (like Maven Central, npm registry, etc.). When you request an artifact, Nexus will download it from the remote source and cache it locally.
- Hosted Repositories: These are local repositories where you can store your own internally developed artifacts.
- Virtual Repositories (Repository Groups): These combine multiple repositories (proxy, hosted, or other groups) into a single repository, simplifying access to multiple sources.
## Supported Formats
Nexus supports a wide range of repository formats:

- Maven (Java): Stores Java libraries (JAR files) following the Maven structure.
- npm (Node.js): Manages Node.js packages.
- NuGet (.NET): For .NET assemblies and libraries.
- PyPI (Python): Manages Python packages.
- Docker: Stores Docker images in private registries.
- RubyGems (Ruby): Stores Ruby gems.
## Key Features
- Artifact Storage: Nexus can store software packages and artifacts such as JARs, WARs, ZIPs, and other binaries.
- Dependency Management: It helps in managing dependencies for builds, especially in Maven and Gradle-based projects.
- Versioning: Nexus supports multiple versions of artifacts, making it easy to retrieve the correct version during builds.
- Access Control: It provides fine-grained security with role-based access control (RBAC) for users and groups to manage who can publish, retrieve, or manage artifacts.
Search Capabilities: It includes search features for quickly locating artifacts based on various criteria (like name, version, etc.).
## Use Cases
- Build Automation: Nexus integrates with build tools (like Jenkins, Maven, and Gradle) to store and retrieve dependencies during automated builds.
- Caching Artifacts: By proxying external repositories, Nexus helps in speeding up builds and reducing external network dependency by caching dependencies locally.
- Internal Package Sharing: Development teams can use Nexus to store and share their internal packages or libraries securely.
- Release Management: It can handle the release of software versions, storing snapshots and released artifacts separately.
## Popular Versions
- Nexus Repository OSS: The open-source version of Nexus with core repository management features.
- Nexus Repository Pro: The paid version with additional features like advanced security, high availability, and support for more formats.
## Integration with DevOps
- Continuous Integration/Continuous Delivery (CI/CD): Nexus integrates well with CI/CD pipelines, ensuring efficient artifact handling during build and deployment phases.
- Sonatype Nexus IQ: For more advanced security and quality scanning, Sonatype also offers Nexus IQ, which analyzes software components for known vulnerabilities and licensing issues.
## User Interface
Nexus provides an intuitive web-based user interface where you can:

Browse artifacts and metadata.
Manage repository configurations.
View download statistics.
Manage access controls and users.
In summary, Sonatype Nexus is a powerful tool for managing software artifacts, enhancing build efficiency, and ensuring secure and reliable access to software components in both development and production environments.



# Package Promotion

Package promotion in Sonatype Nexus is the process of moving or promoting software components, such as binaries or artifacts, through different stages of development (e.g., from a staging environment to a production environment). It ensures that only verified, tested, and approved artifacts are moved to production or higher stages of the software lifecycle.

Promotion helps automate the release process by ensuring that once an artifact has passed all required checks (e.g., testing, security, and quality scanning), it can be promoted to repositories meant for broader consumption (like production).

## Why is Package Promotion Important?
- Controlled Release Process: Artifacts move through various stages (e.g., development, testing, staging, production) in a controlled way, ensuring stability and quality.
- Security and Quality: Only tested and approved packages are moved to higher-level repositories.
- Continuous Delivery (CD): Fits well with CD pipelines by automating and streamlining the promotion of approved builds.

## Typical Workflow of Package Promotion
The basic steps for package promotion in a Nexus environment are:

- Initial Deployment: The package is deployed to a "snapshot" or "development" repository during the development phase.
- Testing and Validation: Packages in the development repository are tested (unit tests, integration tests, security checks, etc.).
- Promotion to Staging: Once a package passes all tests, it's promoted to a staging repository, where more rigorous tests (e.g., user acceptance tests) may be performed.
- Promotion to Production: If the package passes all necessary checks, it’s finally promoted to the production repository, making it available for general use.
- Archiving/Versioning: Old or deprecated versions are moved to an archive or deprecated repository to keep production clean.
Types of Package Promotion in Nexus
- Snapshot to Release Promotion: Artifacts are first deployed as snapshots (unstable, under-development versions).
Once the artifact is ready for release, it is promoted to a release repository, meaning it's now a stable version.
- Repository-to-Repository Promotion: Packages are promoted from a lower environment's repository (e.g., "staging") to a higher environment's repository (e.g., "production").
This could include moving between repositories like development → staging → production.
Staging Repository in Nexus (Nexus Pro Feature):

In Nexus Repository Pro, the Staging Suite allows you to automatically group artifacts into a staging repository after deployment.
Artifacts are only moved from staging to production after a promotion action (usually triggered by tests passing, manual approval, or automated rules).
Promotion via Nexus REST API
You can promote packages programmatically using Nexus’s REST API. Here’s an example of promoting a package from a staging repository to a production repository:

Retrieve the package from the staging repository:

```bash
curl -u admin:admin123 -X GET 'http://localhost:8081/service/rest/v1/search?repository=staging-repo&group=com.example&name=app&version=1.0'
```
Promote the package to the production repository:
```bash
curl -u admin:admin123 -X POST 'http://localhost:8081/service/rest/v1/components/promote' \
-H 'Content-Type: application/json' \
-d '{
  "targetRepository": "production-repo",
  "componentId": "your-component-id-here"
}'
```
Verify the promotion:

```bash
curl -u admin:admin123 -X GET 'http://localhost:8081/service/rest/v1/search?repository=production-repo&group=com.example&name=app&version=1.0'
```
This basic flow shows how you can move a package from one repository to another programmatically.

## Promotion in a CI/CD Pipeline
In a CI/CD pipeline, package promotion can be automated as part of the workflow. After successful testing and quality checks, the pipeline can trigger the promotion action, ensuring only approved versions are deployed to production.

Here’s how this works:

- Build and Deploy: Jenkins (or another CI tool) builds and deploys an artifact to a development or staging repository in Nexus.
- Run Tests: Automated tests (e.g., unit tests, integration tests) run, checking the artifact’s quality.
- Promote the Artifact: After tests pass, Jenkins triggers a promotion action to move the artifact from staging to production.
 - Deployment: Finally, the artifact in the production repository can be used for deployment to the live environment.
- Promotion with Nexus Staging Plugin (Maven)
For Maven-based projects, Sonatype Nexus provides a Maven Nexus Staging plugin that can handle the promotion process seamlessly.

- Deploying with Staging: When you deploy artifacts using the Maven Staging Plugin, Nexus automatically creates a staging repository.

```bash
mvn clean deploy -P release
```
Closing a Staging Repository: After the artifact is deployed, you can close the staging repository using the plugin or the Nexus UI. Closing ensures that the repository is frozen and cannot be modified.

```bash
mvn nexus-staging:close
```
Promoting (Releasing) a Staging Repository: Once you’re satisfied with the artifacts in the staging repository, you can promote (release) it to production.

```bash
mvn nexus-staging:release
```
Dropping a Staging Repository: If there’s an issue with the package in staging, you can drop the repository to delete the artifacts.

```bash
mvn nexus-staging:drop
```
## Key Benefits of Package Promotion
- Quality Assurance: Only approved and tested artifacts move to higher stages (staging/production).
- Traceability: Tracks the lifecycle of an artifact from development to production, ensuring visibility.
- Automation: Automates the promotion process, integrating with CI/CD systems for efficient delivery.
- Security: Avoids untested or unverified packages being deployed to production environments.

## Conclusion
Package promotion in Nexus plays a vital role in ensuring software components move through a well-defined, secure, and automated pipeline from development to production. It reduces the risk of deploying unstable or vulnerable artifacts and ensures a smooth, traceable, and consistent release process.




# Using API and cli 

Here are some common Sonatype Nexus CLI commands using Nexus's REST API and command-line tools like cURL or nexus3-cli. Nexus provides REST API endpoints to interact with the repository manager programmatically.

## Using cURL with Nexus REST API
### List all repositories
```bash
curl -u admin:admin123 -X GET 'http://localhost:8081/service/rest/v1/
repositories'
```
This command will return a list of all repositories in Nexus Repository Manager.

### Create a new Maven hosted repository
```bash
curl -u admin:admin123 -X POST 'http://localhost:8081/service/rest/v1/
repositories/maven/hosted' \
-H 'Content-Type: application/json' \
-d '{
  "name": "my-maven-repo",
  "online": true,
  "storage": {
    "blobStoreName": "default",
    "strictContentTypeValidation": true,
    "writePolicy": "ALLOW"
  }
}'
```
This creates a new Maven repository named my-maven-repo.

### Upload an artifact to a hosted repository
```bash
curl -u admin:admin123 -X PUT 'http://localhost:8081/repository/
my-maven-repo/com/example/app/1.0/app-1.0.jar' \
--upload-file ./app-1.0.jar
```
This uploads a JAR file to the Maven repository my-maven-repo.

### Delete an artifact
```bash
curl -u admin:admin123 -X DELETE 'http://localhost:8081/repository/
my-maven-repo/com/example/app/1.0/app-1.0.jar'
```
This deletes an artifact (in this case, app-1.0.jar) from the my-maven-repo.

### Delete a repository
```bash
curl -u admin:admin123 -X DELETE 'http://localhost:8081/service/rest/v1/
repositories/my-maven-repo'
```
This deletes the repository named my-maven-repo.

## Using nexus3-cli
The nexus3-cli is a dedicated command-line tool for interacting with Sonatype Nexus.

### Install nexus3-cli
```bash
pip install nexus3-cli
```
### Login to Nexus
```bash
nexus3 login http://localhost:8081 -u admin -p admin123
```
This command logs you into the Nexus server.

### List all repositories
```bash
nexus3 repo list
```
### Create a hosted Maven repository
```bash
nexus3 repo create-hosted maven my-maven-repo
```
This command creates a new hosted Maven repository called my-maven-repo.

### Upload an artifact to the repository
```bash
nexus3 upload ./app-1.0.jar my-maven-repo/com/example/app/1.0/app-1.0.jar
```
This uploads the JAR file to the my-maven-repo repository.

### Delete a repository
```bash
nexus3 repo delete my-maven-repo
```
## REST API with Authentication Token
If you're using token-based authentication, the Authorization header will change slightly:

### Get Authentication Token
You can generate an authentication token via the Nexus web UI or use basic auth for REST API commands.

### Using Authentication Token
```bash
curl -H "Authorization: Bearer your_token_here" \
-X GET 'http://localhost:8081/service/rest/v1/repositories'
```
## Other Useful CLI Examples
### Search for an artifact
Search for a Maven artifact by its group ID, artifact ID, and version:

```bash
curl -u admin:admin123 -X GET 'http://localhost:8081/service/rest/v1/search?
repository=my-maven-repo&group=com.example&name=app&version=1.0'
```
### Backup Nexus Configuration
```bash
curl -u admin:admin123 -X POST 'http://localhost:8081/service/rest/v1/script/
backup/run'
```
This will trigger a backup if a backup script is configured in Nexus.

### Check Nexus System Status
```bash
curl -u admin:admin123 -X GET 'http://localhost:8081/service/rest/v1/status'
```
This checks the current system status of your Nexus Repository Manager.

These examples demonstrate how you can manage Sonatype Nexus via the command line for repository creation, artifact management, and administrative tasks. Be sure to replace credentials and URLs as needed, and note that Nexus's REST API can be used for many additional operations.
