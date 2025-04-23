pipeline {
    agent any
    
    environment {
        // Environment variables
        NODE_VERSION = '18'  // Specify which Node.js version to use
        NEXT_PUBLIC_API_URL = 'https://api.example.com'  // Example environment variable for Next.js
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Get the code from the repository
                checkout scm
            }
        }
        
        stage('Setup Node.js') {
            steps {
                // Setup Node.js environment
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # Load NVM
                    
                    # Install Node.js if not available
                    if ! command -v node &> /dev/null; then
                        curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
                        . "$NVM_DIR/nvm.sh"
                        nvm install ${NODE_VERSION}
                        nvm use ${NODE_VERSION}
                    fi
                    
                    # Print versions for verification
                    node -v
                    npm -v
                '''
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    
                    # Install dependencies
                    npm ci
                '''
            }
        }
        
        stage('Lint') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    
                    # Run linting if available
                    if grep -q "lint" package.json; then
                        npm run lint
                    else
                        echo "No lint script found, skipping"
                    fi
                '''
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    
                    # Run tests if available
                    if grep -q "test" package.json; then
                        npm test
                    else
                        echo "No test script found, skipping"
                    fi
                '''
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    
                    # Build the Next.js application
                    npm run build
                '''
            }
        }
    }
    
    post {
        always {
            // Clean workspace after build
            cleanWs()
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed!'
            // You could add notification steps here (email, Slack, etc.)
        }
    }
}