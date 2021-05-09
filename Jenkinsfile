// 镜像仓库地址
def registry = "harbor.scm.local"

// 项目&镜像配置信息
def namespace = "jian"
def app_name = "spring"
def image_name = "${registry}/${namespace}/${app_name}:${BUILD_NUMBER}"
def git_address = "git@192.168.11.161:qujian/spring-mysql-redis-cache.git"

// 认证信息ID
def docker_registry_auth = "dockerHub"
def git_auth = "64ebab93-e58b-4ced-acc2-993029390661"
def secret_name = "registry-secret"
def k8s_auth = "kubeconfig"



podTemplate(label: 'jenkins-slave', cloud: 'kubernetes', containers: [
    containerTemplate(
        name: 'jnlp', 
        //image: "${registry}/${namespace}/jenkins-slave-jdk:1.8",
        image: "harbor.scm.local/jian/jenkins-jnlp:v1"
    ),
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker'),
    persistentVolumeClaim(mountPath: '/root/.m2/repository', claimName: 'maven-pv-claim', readOnly: false),
    persistentVolumeClaim(mountPath: '/home/jenkins', claimName: 'slave-pv-claim', readOnly: false)
  ],
)
{
  node("jenkins-slave"){
      stage('拉取代码'){
         checkout([$class: 'GitSCM', branches: [[name: '${Branch}']], userRemoteConfigs: [[credentialsId: "${git_auth}", url: "${git_address}"]]])
      }
      stage('代码编译'){
          sh "mvn clean package -Dmaven.test.skip=true"
      }
      stage('构建镜像'){
          withCredentials([usernamePassword(credentialsId: "${docker_registry_auth}", passwordVariable: 'password', usernameVariable: 'username')]) {
            sh label: '', script: '''              echo \'
                FROM openjdk:8 
                ADD target/*.jar / 
                ADD entrypoint.sh /
                RUN chmod +x /entrypoint.sh
                CMD ["/bin/bash","/entrypoint.sh"]
              \' > Dockerfile
              echo \'
                #!/bin/bash
                app=`ls /*.jar`
                java -jar $app
                \' > entrypoint.sh'''
            sh """
              docker build -t ${image_name} .
              docker login -u ${username} -p \"${password}\" ${registry}
              docker push ${image_name}
              """
            }
      }
      stage('部署到K8S'){
          sh """
          sed -i 's#\$IMAGE_NAME#${image_name}#' spring.yaml
          sed -i 's#\$SECRET_NAME#${secret_name}#' spring.yaml
          """
          kubernetesDeploy configs: 'spring.yaml', kubeconfigId: "${k8s_auth}"
      }
  }
}
