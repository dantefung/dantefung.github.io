title: Druid数据库连接池数据库密码加密
author: dantefung
tags:
  - 持久层
categories:
  - JavaEE
date: 2018-11-19 12:49:00
---
进入到druid-1.0.27.jar文件下载的目录，执行以下操作
    java -cp druid-1.0.27.jar com.alibaba.druid.filter.config.ConfigTools you_password
    下面是例子
    java -cp druid-1.0.27.jar com.alibaba.druid.filter.config.ConfigTools 123456
   回车之后得到下面的数据

私钥
  privateKey:MIIBVQIBADANBgkqhkiG9w0BAQEFAASCAT8wggE7AgEAAkEAovl027wL8dFaWuTeIsVXS
 LYxKEjKwCLZlWiYq26cVPpINih1BWPSdQ5y5JzlcEeYKdLhmFjIbtEW44+zYfzOgwIDAQABAkBL0PDkK
B8GZ/Y4wq/0TQnF8bv8maB4sb23G+mqFtLzVJrE3qX+3VU5MO0Ac43krZ6N3XZ6Sg6O8M3LijNMj1MBA
 iEA8xasZ0e72aUWEBjNDYDmqPyb1MouZ7d4rW1vEYBR6dMCIQCroXRN+uQO7M2trv1VjGbiuF2jeoeKf
 XJeV1yg4mJqkQIgbPMjkCKhefm4urwy4zN7rg+R1DxNsiSUDL64ghKOJAsCIQCUEwHi3VixP8Db3iOXE
 UEazfGLv4T9COoJVtoFvAso4QIhAKgbsS66Gl8iLI8QkXyAG0oYam/KDBi4sQDrVBuaeKuu

公钥
 publicKey:MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAKL5dNu8C/HRWlrk3iLFV0i2MShIysAi2ZVomK
 tunFT6SDYodQVj0nUOcuSc5XBHmCnS4ZhYyG7RFuOPs2H8zoMCAwEAAQ==

加密后的密码
 password:cRMYiJ6jH6BKIJAiu/NU54Z15wJdn3qcbdNL4gkICwgMdjvN0QyHs6+6a55Wynh3Prxr10V
 2t96oQI1gXXhVRQ==

配置数据源，提示Druid数据源需要对数据库密码进行解密。
   在application.properties文件中
   spring.datasource.password=加密后的密码
   spring.datasource.publickey=公钥

配置参数，让ConfigFilter解密密码
  可以在配置文件application.properties中指定config.decrypt=true 
 在DruidDBConfig.java中进行调用ConfigTools.decrypt(publicKey, password)进行解密