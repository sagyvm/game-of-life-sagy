//Picking a build agent labeled "ec2" to run pipeline on
node ('node_1'){
  stage 'Pull from SCM'  
  //Passing the pipeline the ID of my GitHub credentials and specifying the repo for my app
  git credentialsId: '90107d42-aafc-8691-4760-fcfe3594bd50', url: 'https://github.com/sagyvm/game-of-life-sagy.git'
  //stage 'Test Code'  
  //sh 'mvn install'

  stage 'Build app' 
  //Running the maven build and archiving the war
  sh 'mvn install'
  archive 'target/*.war'
  
  stage 'Package Image'
  //Packaging the image into a Docker image
  //sh 'sudo docker ps'
  //sh 'sudo docker build -t sagydocker/game-of-life'
  def pkg = docker.build ('sagydocker/game-of-life', '.')

  
  stage 'Push Image to DockerHub'
  //Pushing the packaged app in image into DockerHub
  docker.withRegistry ('https://index.docker.io/v1/', 'ed17cd18-975e-4224-a231-014ecd23942b') {
      sh 'ls -lart' 
      pkg.push 'docker-demo'
  }
  
  stage 'Stage image'
 
        sh "sudo aws ecs update-service --service docker-serv  --cluster staging --desired-count 0"
        timeout(time: 5, unit: 'MINUTES') {
            waitUntil {
                sh "sudo aws ecs describe-services --services docker-serv  --cluster staging  > .amazon-ecs-service-status.json"

                // parse `describe-services` output
                def ecsServicesStatusAsJson = readFile(".amazon-ecs-service-status.json")
                def ecsServicesStatus = new groovy.json.JsonSlurper().parseText(ecsServicesStatusAsJson)
                println "$ecsServicesStatus"
                def ecsServiceStatus = ecsServicesStatus.services[0]
                return ecsServiceStatus.get('runningCount') == 0 && ecsServiceStatus.get('status') == "ACTIVE"
            }
        }
        sh "sudo aws ecs update-service --service docker-serv  --cluster staging --desired-count 1"
        timeout(time: 5, unit: 'MINUTES') {
            waitUntil {
                sh "sudo aws ecs describe-services --services docker-serv --cluster staging > .amazon-ecs-service-status.json"

                // parse `describe-services` output
                def ecsServicesStatusAsJson = readFile(".amazon-ecs-service-status.json")
                def ecsServicesStatus = new groovy.json.JsonSlurper().parseText(ecsServicesStatusAsJson)
                println "$ecsServicesStatus"
                def ecsServiceStatus = ecsServicesStatus.services[0]
                return ecsServiceStatus.get('runningCount') == 0 && ecsServiceStatus.get('status') == "ACTIVE"
            }
        }
        timeout(time: 5, unit: 'MINUTES') {
            waitUntil {
                try {
                    sh "curl http://34.211.149.83:80"
                    return true
                } catch (Exception e) {
                    return false
                }
            }
        }
        echo "gameoflife#${env.BUILD_NUMBER} SUCCESSFULLY deployed to http://34.211.149.83:80"
        input 'Does staging http://34.211.149.83:80 look okay?'
  
  stage 'Deploy to ECS'
  //Deploy image to production in ECS
        sh "aws ecs update-service --service production-deploy-game  --cluster production --desired-count 0"
        timeout(time: 5, unit: 'MINUTES') {
            waitUntil {
                sh "aws ecs describe-services --service production-deploy-game  --cluster production   > .amazon-ecs-service-status.json"

                // parse `describe-services` output
                def ecsServicesStatusAsJson = readFile(".amazon-ecs-service-status.json")
                def ecsServicesStatus = new groovy.json.JsonSlurper().parseText(ecsServicesStatusAsJson)
                println "$ecsServicesStatus"
                def ecsServiceStatus = ecsServicesStatus.services[0]
                return ecsServiceStatus.get('runningCount') == 0 && ecsServiceStatus.get('status') == "ACTIVE"
            }
        }
        sh "aws ecs update-service --service production-deploy-game  --cluster production  --desired-count 1"
        timeout(time: 5, unit: 'MINUTES') {
            waitUntil {
                sh "aws ecs describe-services --service production-deploy-game  --cluster production  > .amazon-ecs-service-status.json"

                // parse `describe-services` output
                def ecsServicesStatusAsJson = readFile(".amazon-ecs-service-status.json")
                def ecsServicesStatus = new groovy.json.JsonSlurper().parseText(ecsServicesStatusAsJson)
                println "$ecsServicesStatus"
                def ecsServiceStatus = ecsServicesStatus.services[0]
                return ecsServiceStatus.get('runningCount') == 0 && ecsServiceStatus.get('status') == "ACTIVE"
            }
        }
        timeout(time: 5, unit: 'MINUTES') {
            waitUntil {
                try {
                    sh "curl http://34.211.143.161:80"
                    return true
                } catch (Exception e) {
                    return false
                }
            }
        }
        echo "gameoflife#${env.BUILD_NUMBER} SUCCESSFULLY deployed to http://34.211.143.161:80"
    }
  
