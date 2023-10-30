# SpringSecurity

## 一、基本概念

SpringSecuirty  用户登陆系统时，我们协助Spring Security 把用户对应的角色、权限封装好，同事把哥哥字义安所要求的权限给设定好，剩下的，“登陆验证”，“权限验证”等等工作交给Spring S e cu ri t y 来完成；

## 二、权限管理构成中的相关概念

​	**主体：**principal，使用系统的用户或设备或从其他系统远程登录的用户等等。简单说就是谁使用系统

谁就是主体

​	**认证：**authentication，权限管理系统确认一个主体的身份，允许主体进入系统。简单说就是“主体”证明

自己是谁。笼统的认为就是以前所做的登录操作。

​	**授权：**authorization，将操作系统的“权力”“授予”“主体”，这样主体就具备了操作系统中特定功能的

能力。所以简单来说，授权就是给用户分配权限。



权限管理的主流框架： **SpringSecurity,Shrio**

Shrio 是Tomcat ，轻量级；

SpringSecurity 是Spring的，重量级的；