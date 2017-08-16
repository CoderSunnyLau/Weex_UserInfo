# 搭建weex开发环境
一、安装node.js  
      可到官网下载：[https://nodejs.org/en/](https://nodejs.org/en/)  
      
二、安装weex-toolkit  
    可直接用npm安装： <code>npm install -g weex-toolkit</code>  
    安装完成后，可用 weex 命令验证是否安装成功，它会显示 weex 命令行工具各参数：  
    ![Alt text](https://img.alicdn.com/tfs/TB1NBhdQXXXXXXzXFXXXXXXXXXX-712-343.png)

三、运行weex项目  
    在命令提示符中，打开到项目的目录下，例： D:\weex\userInfo>  
    然后安装项目依赖： <code>npm install</code>  
    安装完成后用以下命令来运行项目：
    <code>npm run dev</code> , 
    <code>npm run serve</code>  
    最后在浏览器打开： [http://localhost:8080/](http://localhost:8080/) ，即可查看运行效果，亦可用手机上的weex playground扫描页面上的二维码来预览（注：手机需和电脑使用同一网络）。    
    效果如下：  
    ![Alt text](http://file.ry600.com/snapshot//files/af/afvnal1p3sa59q79/2017-06-01/55vh5yv98ydhotey.png)


详细步骤请参考： [https://weex.apache.org/cn/guide/set-up-env.html](https://weex.apache.org/cn/guide/set-up-env.html)
