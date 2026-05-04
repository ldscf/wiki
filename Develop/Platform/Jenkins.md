---
source_title: Jenkins
categories:
- Develop
- Platform
last_modified: '2024-12-02T07:58:42Z'
---
Jenkins 是一个开源的、可扩展的自动化服务器，广泛用于[[CI CD|持续集成和持续交付]]的流水线中。

### 概述

Jenkins 可以对版本控制系统中的代码实现自动化构建、测试和部署。 
1. 编译代码：将代码编译成可执行文件或其他形式的交付物
1. 运行测试：执行单元测试、集成测试等
1. 打包部署：将构建好的软件打包成安装包，并完成部署

通过自动化执行重复性的任务，提高开发效率，减少人为错误，加速软件交付。

### Inst
1. 系统要求
- 256 MB RAM
- 1GB Space
- Java 8 以上
2. Linux Debian
```
 wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
```

sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

apt update

apt install jenkins

# 将创建一个 jenkins 用户来运行此服务
* Daemon: /etc/init.d/jenkins
* Config: /etc/default/jenkins
* Log   : /var/log/jenkins/jenkins.log
* Passwd: /var/lib/jenkins/secrets/initialAdminPassword (admin)
```
 http://Jenkins_host:8080/
```

### Manage

#### 节点

Jenkins 运行的主机为 master 节点（名字一般不可修改）。

Dashboard -> Manage Jenkins -> [System Configuration]Nodes (Ver: 2.479.1)
* Permanent Agent: 常驻代理节点，可以一直保持连接状态的节点，用来执行 CI/CD 任务，在 pipeline/state 中用 agent 指定。
* Dumb Slave: 每次构建任务开始时启动，任务结束后关闭
* Docker Slave: 基于 Docker 容器的节点，可以快速创建和销毁不同环境的节点
* Kubernetes Slave: 基于 Kubernetes 的节点，可以利用 k8s 的强大功能进行节点管理

### Pipeline

Jenkins Pipeline 是一种用于实现持续交付的工具插件，它提供了一种灵活的方式来定义和执行一系列的步骤，从构建、测试到部署。

# Agent: 指定 Pipeline 或 stage 运行的节点

# Stage: 流水线被划分为多个阶段，每个阶段代表一个特定的构建步骤，如构建、测试、部署等（可嵌套）

# Step : 每个 stage 由多个 step 组成，step 是最小的执行单元，如运行 [[Shell基础|shell 脚本(Linux Shell)]]、执行 script 脚本(Jenkins script)等

#### parallel
 ```
pipeline {
    agent any
    stages {
        stage('PARALLEL BUILD') {
            parallel {
                stage('BUILD A') {
                    steps {
                        sh 'mvn clean package -pl moduleA'
                    }
                }
                stage('BUILD B') {
                    steps {
                        sh 'mvn clean package -pl moduleB'
                    }
                }
            }
        }
    }
}
```

#### Sample
 ```
pipeline {
    agent {
        label 'node_test'
    }
    options {
        timeout(time: 1, unit: 'HOURS') 
    } 
    parameters { 
        string(name: 'GIT_URL', defaultValue: 'http://IP/XX.git', description: '--> git url <--') 
        string(name: 'GIT_BRANCH', defaultValue: 'master', description: '--> code branch <--')
        string(name: 'autotest_GIT_URL', defaultValue: 'http://ip/XX_test.git', description: '-->autotes git url <--') 
    }
    environment {
        GIT_TARGET_DIR_NAME="XX_V1.0.0"
        JAVA_HOME=""
    }
    stages{ 
        stage('Git Code') { 
            steps {
                sh '''
                    sleep 6.8
                '''
            }
        } 
        stage('PARALLEL BUILD') { 
            steps {
                sh '''
                    sleep 36.8
                '''
            }
        }
        stage('source-code-migration') {
            agent {
                label 'node_test'
            }
            steps {
                sh '''
                    rm -rf /home/test/report_dir/src-mig*.html
                '''
                script{
                    def STATUS_CODE = sh(returnStatus: true, script: '''
                                      devkit porting src-mig -i "/home/test/XX_V1.0.0" -s interpreted  -r html -o /home/test/report_dir
                                        ''')
                   switch(STATUS_CODE) {
                        case 0:
                            echo '【源码迁移】--> 无扫描建议 <--'
                            break
                        default:
                            currentBuild.result = 'ABORTED'
                            echo '【源码迁移】--> 异常终断开{Ctrl + C | Ctrl + Z} <--'
                            break
                        }
                }
                sh '''
                    html_file_name=$(find /home/test/report_dir -name src-mig*.html)
                    if [[ ${html_file_name} ]]; then 
                        mv ${html_file_name} /home/test/htmlreports/SourceCodeScanningReport.html
                    fi
                '''
            }
            post {
                always {
                    publishHTML(target: [allowMissing: false,
                                alwaysLinkToLastBuild: false,
                                keepAll              : true,
                                reportDir            : '/home/test/htmlreports',
                                reportFiles          : 'SourceCodeScanningReport.html',
                                reportName           : 'Source Code Scanning Report']
                                )
                }
            }
        }
       
        stage('BoostKit-Accelerate') {
            agent {
                label 'node_test'
            }
            steps {
                sh '''
                    rm -rf /home/test/report_dir/pkg-mig*.html
                '''  
                script{
                    def STATUS_CODE = sh(returnStatus: true, script: '''
                                     cd /home/test && rm -rf *.tar.gz && ./collect_msg
                                        ''')
                    sh '''
                     cd /home/test
                     file_name=$(find  ./ -name *.tar.gz)
                        if [[ ${file_name} ]]; then 
                            mv ${file_name} /home/jenkins/workspace/TongRDS_2.2/report_dir/${file_name}
                        fi 
                    '''
                }
                
            }
            post {
                always {
                   sh 'ls -l /home/test/report_dir'
                   archiveArtifacts allowEmptyArchive: true, artifacts: 'report_dir/*.tar.gz', caseSensitive: false, defaultExcludes: false, followSymlinks: false
                }
            }
        }
    
        stage('software-migration-assessment') {
            agent {
                label 'node_test'
            }
            steps {
                sh '''
                    rm -rf /home/test/report_dir/pkg-mig*.html
                '''
                script{
                    def STATUS_CODE = sh(returnStatus: true, script: '''
                                      devkit porting pkg-mig -i "/home/test/XX_V1.0.0" -r html -o /home/test/report_dir
                                        ''')
                    sh '''
                        html_file_name=$(find /home/test/report_dir -name pkg-mig*.html)
                        if [[ ${html_file_name} ]]; then 
                            mv ${html_file_name} /home/test/htmlreports/SoftwareMigrationAssessment.html
                        fi
                        '''
                    switch(STATUS_CODE) {
                        case 0:
                            echo '【软件迁移评估】--> 无扫描建议 <--'
                            break
                        default:
                            currentBuild.result = 'ABORTED'
                            echo '【软件迁移评估】--> 异常终断开{Ctrl + C | Ctrl + Z} <--'
                            break
                    }
                }
            }
            post {
                always {
                    publishHTML(target: [allowMissing: false,
                                alwaysLinkToLastBuild: false,
                                keepAll              : true,
                                reportDir            : '/home/test/htmlreports',
                                reportFiles          : 'SoftwareMigrationAssessment.html',
                                reportName           :  'SoftwareMigrationAssessment Report']
                                )
                }
            }
        }
    }
}
```
