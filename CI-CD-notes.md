## This file Containes the important notes for ci/cd pipelines and github actions.

 ## Standard structure of GitHub actions CI/CD Yaml Pipeline
 # 1. Name of the workflow
name: CI/CD Pipeline

## 2. The event that triggers the workflow
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

## 3. List of jobs to run
jobs:
  build-and-test:
    # The type of machine to run on
    runs-on: ubuntu-latest

    # 4. Sequence of tasks (steps)
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Environment
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm install

      - name: Run Tests
        run: npm test

  deploy:
    # Only runs if the previous job succeeds
    needs: build-and-test
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production
        run: echo "Deploying application..."

## Structure explanation of GitHub Actions YAML
 - " name " : Workflow Name
    - It defines the name of the workflow that appears in the GitHub Actions dashboard.
    - It helps identify the workflow among multiple pipelines.
  
- " on " : Trigger Section
    - It defines events that trigger the workflow.
    - | Trigger           | Purpose             |
      | ----------------- | ------------------- |
      | push              | when code is pushed |
      | pull_request      | when PR is created  |
      | schedule          | cron jobs           |
      | workflow_dispatch | manual trigger      |
    - This means the pipeline runs whenever a pull request is created for the main branch.

- " jobs " : Jobs Section
    - A job represents a unit of work in the workflow.
    - Each job runs on a separate virtual machine (runner) provided by GitHub.
    - Jobs can run in parallel
    
- " runs-on " : Runner Environment
    - It specifies the operating system environment where the job will run.
    - eg. ubuntu , windows , macos
    - Every job needs a seperate runner , it's called parallelism

- " needs " : Job dependencies
    - It ensures that a job runs only after another job completes successfully.
    - build → test → deploy

- " steps " : Steps section
    - Steps define the individual tasks executed inside a job.
    - Each step runs sequentially.

# Full CI/CD Pipeline Example

name: CI Pipeline

on:
  push:
    branches: [main]

jobs:
  
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install Dependencies
        run: npm install

      - name: Build Application
        run: npm run build

  test:
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Run Tests
        run: npm test

  deploy:
    runs-on: ubuntu-latest
    needs: test

    steps:
      - name: Deploy Application
        run: echo "Deploying application"