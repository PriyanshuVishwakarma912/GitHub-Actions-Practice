## This file Containes the important notes for ci/cd pipelines and github actions.

 # Standard structure of GitHub actions CI/CD Yaml Pipeline
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

# Note points
- Every job needs seperate runner, its called parallelism