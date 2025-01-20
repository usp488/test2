### Homework Assignment: Building a CI/CD Pipeline with GitHub Actions

#### **Objective**
Students will design and implement a CI/CD pipeline using **GitHub Actions** for a **Ruby application**. The pipeline will ensure code quality, security, and proper deployment practices by including steps for linting, vulnerability scanning, testing, and Docker image deployment.

---


#### **Prerequisites**
1. **Knowledge Required**:
   - Basics of GitHub Actions
   - Familiarity with Ruby, unit testing, Docker, and GitHub secrets

2. **Tools**:
   - A GitHub repository (students' own or a provided template)
   - A Docker Hub account (or any container registry)

---

#### **Assignment Tasks**

1. **Clone the Template Repository**:
   - A sample Ruby application with unit tests will be provided.
   - Students must clone the repository and set it up locally.

2. **Implement GitHub Actions Workflow**:
   - Create two workflow files in the `.github/workflow/` directory (You'll need to create the directory also):
     1. **`main.yml`** for pushes to the `main` branch.
     2. **`develop.yml`** for pushes to the `develop` branch.

3. **Pipeline Steps**:
   Each workflow should include the following steps:

   **Step 1: Setup**
   - Use the latest Ubuntu image for the runner.
   - Set up Ruby using the `ruby/setup-ruby@v1` GitHub Action.

     Example setup:
     ```yaml
     steps:
       - name: Setup Ruby
         uses: ruby/setup-ruby@v1
         with:
           ruby-version: '3.1'
     ```

   **Step 2: Setup Depdencies**
   - Add a step that installs the dependencies for your Ruby project.
   - **Hint:** Most often this is done using the `bundle` tool to install Gems. Some docs exist at [Gem Installation Guide](./Docs/Gem-Installation-Guide.md)
   
   **Step 3: Linting and Styling**
   - Add a step to check for code styling and linting using `rubocop`.
   - **Hint:** Refer to the [docs here for how RuboCop is used](./Docs/Using-Rubocop.md).

   **Step 3: Vulnerability Checks**
   - Use `bundler-audit` to check for dependency vulnerabilities.
   - Be sure to update `bundler-audit`'s database of vulnerabilities before doing the check.
   - **Hint**: Note that it will check your Gemfile (dependencies). There's only one Gem that specifies a version to install, the other Gems will be installed to the latest compatible version, i.e. if `sinatra` is specified at version 1 and depends on `rack` and `rack`'s version isn't specified then rack will be installed to the latest version most compatible with `sinatra`.
   - **Hint**: If you encounter high numbers of vulnerabilities then you may only need to change one of the dependencies.

   **Step 4: Unit Tests**
   - Execute unit tests for the Ruby application using `rspec`.
   - **Hint:** Refer to the [docs here for how to use rspec](./Docs/Using-Rspec.md).

   **Step 5: Docker Image Build and Push**
   - Build the Docker image for the Ruby application.
   - Push the image to a Docker registry (e.g., Docker Hub).
   - Requimrenets for your `Dockerfile` are below:

     **Requirements for Building the Docker Image**:
     1. Create a `Dockerfile` in the project root directory.
     2. Use a Ruby base image appropriate for the app's requirements.
        - **Hint**: Look through dockerhub for a ruby image to use as your base, likely best to stick to popular images or official/verified images.
     2. Setup a workspace inside the container where your app will live.
     3. Copy the necessary files into the container
     4. Install your application dependencies.
        - **Hint**: In ruby dependencies are known as Gems and typically are defined in a `Gemfile`. This is the equivalent of `package.json` for NodeJS style apps, `requirements.txt` for python apps, nuget package references in `.csproj` files for .NET, and so on.
        - **Hint**: You'll notice that if you try to run the app then there are two packages missing, namely `rackup` and `puma`. Resolve these missing dependencies.
     4. Expose the port the app will run on (e.g., `4567` for Sinatra).
     5. The default command for this image should by to use execute `ruby app.rb -o 0.0.0.0`.
        - **Hint**: This should start up the equivalent of an API similar to flask that you can nagivate to.
     5. Test the Docker image locally using `docker build` and `docker run`.
     6. Configure the GitHub Action to build the Docker image using the `docker build` command.
     7. Push the Docker image to Docker Hub using GitHub secrets for authentication (`DOCKER_USERNAME`, `DOCKER_PASSWORD`).

     **WARNING**: Do not include your credentials in the pipeline file. Use GitHub secrets for secure handling of sensitive information.

4. **Environment Variables and Secrets**:
   - Configure GitHub repository secrets for:
     - Docker Hub credentials (`DOCKER_USERNAME`, `DOCKER_PASSWORD`)

5. **Branch-Specific Behavior**:
    - Ensure `main.yml` workflow deploys the Docker image with the `latest` tag.
        - This should be considered your "production" environment for your app and should be triggered by pushes to branch `main`
   - Ensure `develop.yml` workflow deploys the Docker image with the `develop` tag.
        - This should be considered your "develop" environment for your app and should be triggered by pushes to branch `develop`

---
### Deliverables
**Submit the Assignment**:
   - Submit the GitHub repository URL.
   - Submit the link to your Docker Hub repo with the image.

### **Grading Criteria**

1. **Correctness**:
   - All required steps are present and functional.
   - Workflows execute without errors (passing pipeline).
   - Docker image for both `main` and `develop` can be pulled and executed successfully.

2. **Best Practices**:
   - Proper use of GitHub Actions environment variables and secrets.
   - Clear and maintainable workflow files.
   - **WARNING: Zero points if you hardcode your credentials in your pipeline file**.

3. **Bonus**:
    - 10pts: In addition to tagging and pushing two images with the `develop` and `main` tags, successfully tag and push a Docker image using the short version of the git commit hash that triggered the pipeline.
        - **Outcome**: Have an image in your repo named/tagged as `<your username>/ruby-app:243883d`.
    - 5pts: Explain why tagging an image using the git commit is a good idea. Your answer  be evaluated for AI inspiration :) 
