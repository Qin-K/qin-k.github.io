---
title: 'jenkins入门介绍, 常见应用集成'
date: 2019-07-08 14:42:03
categories: 工具
tags: 
	- jenkins
	- devops 
---

## 什么是Jenkins

- Jenkins是一个开源持续集成工具
- 开发工具：JAVA
- 功能：提供了软件开发的持续集成服务
- 特点：支持主流软件配置管理，配合实现软件配置管理，持续集成功能。

## Jenkins的优势和应用场景

- 主流的运维开发平台，兼容所有主流开发环境
- 插件市场可与海量业内主流开发工具实现集成
- Job为配置单位与日志管理，是运维与开发人员能协调工作
- 权限管理划分不同Job不同角色
- 强大的负载均衡功能，保证我们项目的可靠性

## Jenkins安装配置管理

### 安装Jenkins前的环境准备(CentOS7)

1. 添加yum仓库源

   ```shell
   wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
   ```

   ```shell
   rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
   ```

2. 保证系统JAVA版本为8.0或8.0以上

3. 关闭防火墙

4. 关闭SELINUX并重启系统

   ```
   vim /etc/sysconfig/selinux
   
   ...
   SELINUX=disabled
   ...
   
   reboot
   ```

   查看是否禁用成功：getenforce

### Jenkins安装与初始化配置

1. Yum源安装Jenkins最新版本

   ```shell
   yum install jenkins
   ```

2. 创建Jenkins系统用户

   ```shell
   useradd deploy
   ```

3. 更改Jenkins启动用户与端口

   ```shell
   vim /etc/sysconfig/jenkins
   ...
   JENKINS_USER=deploy
   JENKINS_PORT=8080
   ...
   ```

4. 设置Jenkins家目录和日志目录所属组和人

   ```shell
   chown -R deploy:deploy /var/lib/jenkins
   chown -R deploy:deploy /var/log/jenkins
   ```

5. 启动Jenkins

   ```shell
   service jenkins start
   ```

6. 查看jenkins启动是否成功

   ```shell
   lsof -i:8080
   ```

   <img src="https://ws1.sinaimg.cn/large/006Cx571gy1g51m908a1ej30lz01st8l.jpg"/>

   在浏览器中访问配置的端口,会进入如下界面

   在`/var/lib/jenkins/secrets/initialAdminPassword`查看密码。

   首次登录的时候需要下载一些插件，可以使推荐的插件，也可以自己选择下载。

   自动进入管理账户注册页面，注册好了，进入登录页面。

## Jenkins Job构建

- 代表一个任务或者项目
- 可配置与可执行
- 执行后的记录称之为Build
- 日志监控与记录
- 所有文件集中保存

### Freestyle Job

1. 需在页面添加模块配置项与参数完成配置
2. 每个Job仅能实现一个开发功能
3. 无法将配置代码化，不利于Job配置迁移与版本控制：Job迁移复杂，没有记录。
4. 逻辑相对简单，无需额外学习成本

### Pipeline Job

- 匹配持续集成与持续交付的概念
  - CI持续集成: 代码仓库与Jenkins结合，代码的每次提交都会自动构建。
  - CD持续交付：将构建好的软件版本，持续交付到用户受众。

1. 所有模块，参数配合都可以体现为一个pipeline脚本
2. 可以定义多个stage构建一个管道工作集
3. 所有配置代码化，方便Job配置迁移与版本控制
4. 需要pipeline脚本语法基础

### Jenkins Job构建配置

#### 环境准备

1. 配置Jenkins server本地Gitlab DNS

2. 安装git client, curl 工具依赖

3. 关闭系统Git http.sslVerify安全认证

4. 添加Jenkins后台 Git client usr与email

   系统管理 --> 系统设置 --> 系统设置 -->Git plugin

5. 添加Jenkins后台 Git Credential凭据

   凭据  

#### 构建配置

1. 创建一个Freestyle project（自由风格）

2. 编辑描述信息

3. 参数配置

   - 选项参数: 如选择环境dev prod
   - 文本配置: version

4. 源代码管理

   - 源代码管理：git

     Repository URL, Credentials

5. Build配置

   - 构建， 执行shell

### Jenkins Pipeline Job构建配置

#### Jenkins Pipeline Job编写规范

pipeline 基础架构

- 所有代码包裹在pipeline{}层内
- stages{}层用来包含该pipeline所有stage子层
- stage{}层用来包含具体我们需要编写任务的steps{}子层
- steps{}层用来添加我们具体需要调用的模块语句

agent区域

- agent定义pipeline在哪里运行
  可以使用any，none，或具体的Jenkins node主机名等。

  例如：特指在node1上执行 agent {node {label 'node1'}}

environment区域

- 变量名称=变量值“定义我们的环境变量”
- 可以定义全局环境变量，应用所有stages任务
- 可以定义stage环境变量，应用单独的stage任务

script区域(可选)

- 在steps内定义script{}
- groovy脚本语言
- 用来进行脚本逻辑运算

常用steps区域

- echo：打印输出
- sh：调用Linux系统shell命令
- git url: 调用git模块进行git相关操作

parameters选项参数

- choice(choices, description, name) 选项参数
- string(name, defalutValue, description) 文本参数

```json
pipeline{
    agent any
    environment{
    	PATH="/bin:/sbin:/usr/bin:/usr/sbin"
        host='test.example.com'
        user='deploy'
    }
    
    stages{
        stage('build') {
            enviroment{
                JAVA_HOME="usr/local/java"
            }
            steps{
                sh "cat $host"
                echo $deploy
                
                script{
                    
                }
            }
        }
    }
    
}
```

#### Jenkins Pipeline Job构建配置

1. 创建一个Pipeline project（流水线）

2. 编辑描述信息

3. 编写Pipeline脚本

   ```json
   #!groovy
   
   pipeline {
   	agent {node {label 'master'}}
   	
   	environment {
   		PATH="/bin:/sbin:/usr/bin:/usr/sbin:usr/local/bin"
   	}
   	
   	parameters{
   		choice(
   			choices: 'dev\nprod',
   			description: 'choose deploy environment',
   			name: 'deploy_env'
   		)
   		string (name: 'version', defaultValue: '1.0.0', description: 'build version')
   	}
   	
   	stages {
   	
   			stage("Checkout test repo") {
   				steps{
   					sh 'git config --global http.sslVerify false'
   					dir ("${env.WORKSPACe}") {
   						git branch: 'master', url: 'ssh://git@git.idwzx.com:7999/deo/qinkai.git'
   					}
   				}
   			}
   			stage("Print env variable") {
   				steps {
   					dir ("${env.WORKSPACe}") {
   						sh """
   						echo "[INFO] print env variable"
   						echo "current deployment environment is $deploy_env" >> test.properties
   						echo "The build is $version" >> test.properties
   						echo "[INFO] Done..."
   						"""
   					}
   				}
   			}
   			
   			stage("Check test properties") {
   				steps{
   					dir ("${env.WORKSPACe}") {
   						sh """
   						echo "[INFO] Check test properties"
   						if [ -s test.properties]
   						then
   							cat test.properties
   							echo "[INFO] Done..."
   						else
   							echo "test.properties is empty"
   						fi
   						"""
   						
   						echo "[INFO] Build finished..."
   					}
   				}
   			}
   			
   	}
   }
   ```

### Jenkins应用

- Jenkins Linux shell集成
  - 构建 -> 增加构建步骤 -> 执行shell
- Jenkins参数集成
  - general - > 参数化构建过程
- Jenkins git 集成
  - Global Tool Configuration-> git (配置Git)
  - 管理Jenkins-> 系统管理 -> git plugin -> 添加name和email
  - 凭据中添加global凭据，输入用户名和密码
  - 源码管理 - > git 添加url、选择凭据
- Jenkins maven集成
  - Global Tool Configuration-> maven (配置maven)
  - 构建->增加构建步骤->选择maven版本，调用Maven顶层目标(package)打包