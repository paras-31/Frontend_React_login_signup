project:
  name: Fullstack React + Node.js Application
  description: |
    A fullstack web application built with React frontend and Node.js Express backend, deployed on AWS Elastic Beanstalk using two separate environments or optionally on EC2.
  features:
    - Signup form UI
    - RESTful API for user signup
    - CORS-enabled communication
    - Hosted on Elastic Beanstalk (recommended) or EC2
    - Easy to scale, modify, and extend

structure:
  - path: frontend/
    description: React frontend application
  - path: frontend/build/
    description: React production build output
  - path: backend/
    description: Node.js Express backend API
  - path: backend/server.js
    description: Main backend server logic
  - path: README.md
    description: Project documentation

local_setup:
  steps:
    - step: Clone Repository
      commands:
        - git clone https://github.com/yourusername/fullstack-react-node-aws.git
        - cd fullstack-react-node-aws
    - step: Start Backend
      directory: backend
      commands:
        - npm install
        - node server.js
      url: http://localhost:5000
    - step: Start Frontend
      directory: frontend
      commands:
        - npm install
        - npm start
      url: http://localhost:3000
    - step: Update Frontend API URL
      file: frontend/src/SignupPage.js
      snippet: |
        await axios.post('http://localhost:5000/api/signup', {
          username,
          password
        });

deployment:
  elastic_beanstalk:
    environments:
      - name: backend-env
        platform: Node.js
        path: backend/
        commands:
          - eb init -p node.js backend-env
          - eb create backend-env
          - eb open
        notes:
          - Ensure CORS is configured in server.js
          - Use process.env.PORT for dynamic port
      - name: frontend-env
        platform: Node.js
        path: frontend/
        pre_deploy:
          - npm run build
          - echo "web: npm install -g serve && serve -s build" > Procfile
        commands:
          - eb init -p node.js frontend-env
          - eb create frontend-env
          - eb open
        notes:
          - Update frontend axios URL to point to backend-env URL

backend_cors_config:
  file: backend/server.js
  snippet: |
    const cors = require('cors');
    app.use(cors({
      origin: 'http://<FRONTEND_ENV_URL>',
      methods: ['GET', 'POST']
    }));

optional_ec2_deployment:
  notes:
    - You can use EC2 instances to manually host the frontend and backend.
    - Install Node.js and NGINX on respective EC2 instances.
    - Use PM2 or forever to run backend.
    - Serve frontend with NGINX pointing to React build directory.
  example_architecture:
    - EC2-Instance-1: Frontend (NGINX + React Build)
    - EC2-Instance-2: Backend (Node.js + Express)

author:
  name: Your Name
  github: https://github.com/yourusername
