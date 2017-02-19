#!/usr/bin/env groovy

node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:lkmadushan/laravel-docker.git'

            // Start services (Let docker-compose build containers for testing)
            sh "./develop up -d --build"

            // Get composer dependencies
            sh "./develop composer install"

            // Create .env file for testing
            sh 'cp .env.example .env'
            sh './develop art key:generate'
            sh 'sed -i "s/REDIS_HOST=.*/REDIS_HOST=redis/" .env'
            sh 'sed -i "s/CACHE_DRIVER=.*/CACHE_DRIVER=redis/" .env'
            sh 'sed -i "s/SESSION_DRIVER=.*/SESSION_DRIVER=redis/" .env'
        }
        stage('test') {
            sh "APP_ENV=testing ./develop test"
        }
    } catch(error) {
        // Maybe some alerting?
        thorw error
    } finally {
        // Spin down containers no matter what happens
        sh './develop down'
        sh 'docker-cleanup'
    }
}
