# CI-CD-Pipeline-with-GitLab-and-Ruby-on-Rails
Let's set up a CI/CD pipeline using GitLab for a Ruby on Rails application. This project involves creating a simple Rails application, configuring GitLab CI/CD, and automating the deployment process.



Let's set up a CI/CD pipeline using GitLab for a Ruby on Rails application. This project involves creating a simple Rails application, configuring GitLab CI/CD, and automating the deployment process.

### Project Outline

1. **Setup GitLab Repository:**
   - Create a GitLab repository for the Rails application.
   - Push your Rails application to the repository.

2. **Configure GitLab CI/CD:**
   - Write a `.gitlab-ci.yml` file to define the CI/CD pipeline.

3. **Deployment:**
   - Use GitLab CI/CD to deploy the application, either to a cloud provider or a local server.

### Detailed Steps

#### Step 1: Setup GitLab Repository

1. Create a new repository on GitLab (e.g., `rails-cicd-app`).
2. Initialize a simple Rails application and push it to the GitLab repository.

**Sample Rails Application:**

Create a new Rails application (if you haven't already):

```sh
rails new rails-cicd-app
cd rails-cicd-app
```

Push the application to the GitLab repository:

```sh
git init
git remote add origin https://gitlab.com/your-username/rails-cicd-app.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

#### Step 2: Configure GitLab CI/CD

Create a `.gitlab-ci.yml` file in the root of your repository.

**.gitlab-ci.yml:**

```yaml
image: ruby:2.7

services:
  - postgres:latest

variables:
  POSTGRES_DB: rails_test
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: password
  RAILS_ENV: test

before_script:
  - apt-get update -qq && apt-get install -y nodejs yarn
  - gem install bundler
  - bundle install
  - cp config/database.yml.ci config/database.yml
  - bin/rails db:create
  - bin/rails db:migrate

stages:
  - test
  - deploy

test:
  stage: test
  script:
    - bundle exec rspec

deploy:
  stage: deploy
  script:
    - echo "Deploying to production server..."
    # Add your deployment script here
  only:
    - master
```

**database.yml.ci:**

Create a `config/database.yml.ci` file for the CI environment.

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: 5
  username: postgres
  password: password
  host: postgres

test:
  <<: *default
  database: rails_test
```

#### Step 3: Deployment

You can customize the `deploy` stage in `.gitlab-ci.yml` to fit your deployment environment. Here are a few examples of deploying to different environments.

**Example 1: Deploy to Heroku**

1. Add Heroku CLI to the `before_script` and configure the deploy script.

```yaml
deploy:
  stage: deploy
  before_script:
    - curl https://cli-assets.heroku.com/install.sh | sh
  script:
    - heroku login
    - git remote add heroku https://git.heroku.com/your-heroku-app.git
    - git push heroku master
  only:
    - master
```

**Example 2: Deploy to AWS EC2**

1. Create a deployment script (`deploy.sh`) to handle the deployment.

**deploy.sh:**

```sh
#!/bin/bash

# SSH into the EC2 instance and deploy the application
ssh -i /path/to/your/key.pem ec2-user@your-ec2-instance-ip << 'EOF'
  cd /path/to/your/app
  git pull origin master
  bundle install
  bin/rails db:migrate RAILS_ENV=production
  bin/rails assets:precompile RAILS_ENV=production
  systemctl restart your-app.service
EOF
```

2. Update the `deploy` stage in `.gitlab-ci.yml` to execute the script.

```yaml
deploy:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - master
```

#### Step 4: Run the Pipeline

1. Push your changes to the GitLab repository.
2. GitLab CI/CD will automatically trigger the pipeline.
3. Monitor the pipeline's progress in the GitLab CI/CD interface.

### Conclusion

By following these steps, you will have created a CI/CD pipeline using GitLab for a Ruby on Rails application. This project demonstrates key DevOps practices such as continuous integration, continuous deployment, and automation. This setup can be further customized to include additional steps such as code quality checks, security scans, and more complex deployment strategies.
