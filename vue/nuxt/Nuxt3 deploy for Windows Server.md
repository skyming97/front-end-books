<a name="SS1nP"></a>
## 打包项目
运行`npm run build`打包项目。打包完成后，Nuxt会在项目根目录生成一个 .output 文件夹，这就是打包产物。<br />![打包命令](https://cdn.nlark.com/yuque/0/2023/png/380273/1701936472388-4383e636-1402-4528-87e8-8689622597b2.png#averageHue=%231c1817&clientId=u2df59f6a-3088-4&from=paste&height=243&id=ue89b4105&originHeight=243&originWidth=369&originalType=binary&ratio=1&rotation=0&showTitle=true&size=16870&status=done&style=none&taskId=u30c7ce56-ae06-4639-ac76-88ec1ef69e1&title=%E6%89%93%E5%8C%85%E5%91%BD%E4%BB%A4&width=369 "打包命令")<br />![打包产物.output](https://cdn.nlark.com/yuque/0/2023/png/380273/1701936624443-10ceab65-20b2-4a08-98b2-312d88bbbddc.png#averageHue=%23191413&clientId=u2df59f6a-3088-4&from=paste&height=126&id=i54z0&originHeight=126&originWidth=148&originalType=binary&ratio=1&rotation=0&showTitle=true&size=3280&status=done&style=none&taskId=u5dcef54c-6112-4356-995a-3f351172bfc&title=%E6%89%93%E5%8C%85%E4%BA%A7%E7%89%A9.output&width=148 "打包产物.output")
<a name="qnHnF"></a>
## 安装Node环境
打开[Node历史版本](https://nodejs.org/en/about/previous-releases)页面，选择一个版本下载。这里选择v16.20.2。进入到下载详情页，选择对应版本，如下图所示。<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/380273/1701937239629-737d5587-554f-4dfe-84f6-756b69d89129.png#averageHue=%23f5f3f2&clientId=u2df59f6a-3088-4&from=paste&height=661&id=u2b335f91&originHeight=661&originWidth=743&originalType=binary&ratio=1&rotation=0&showTitle=false&size=99144&status=done&style=none&taskId=udeef57c8-b903-43b7-93c8-0ea301cc802&title=&width=743)![image.png](https://cdn.nlark.com/yuque/0/2023/png/380273/1701937414610-f32106ad-1874-413a-8dc9-9869efc4844c.png#averageHue=%23f2f1f1&clientId=u2df59f6a-3088-4&from=paste&height=567&id=u9f420dfd&originHeight=567&originWidth=622&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52571&status=done&style=none&taskId=ua3736b06-a360-4073-b0c5-e4a086793b8&title=&width=622)<br />打开系统命令行窗口（cmd），运行以下命令，确保nodejs安装成功。
```powershell
node -v
npm -v
```
![查看node和npm版本](https://cdn.nlark.com/yuque/0/2023/png/380273/1701937621156-667265f0-b5a1-4f0c-b404-207a06c0fe02.png#averageHue=%23353433&clientId=u2df59f6a-3088-4&from=paste&height=192&id=u2259d5d7&originHeight=192&originWidth=323&originalType=binary&ratio=1&rotation=0&showTitle=true&size=9465&status=done&style=none&taskId=u7b66a27d-d3e0-4e2c-af52-95c99597f08&title=%E6%9F%A5%E7%9C%8Bnode%E5%92%8Cnpm%E7%89%88%E6%9C%AC&width=323 "查看node和npm版本")
<a name="MboIX"></a>
## 全局安装pm2
> [什么是pm2？](https://pm2.keymetrics.io/)
> Tips：切记一定要全局安装，这样的话服务器上任意开一个命令窗口都可以使用

运行 `npm install pm2 -g` 全局安装pm2。

<a name="hrDhu"></a>
## 添加pm2配置文件ecosystem.config.js
在根目录（和.output文件放在同一目录下）新建`ecosystem.config.js`，写入以下内容：
```javascript
// 配置pm2
module.exports = {
  apps: [
    {
      name: "SPD360COM",
      exec_mode: "cluster",
      instances: "2",
      script: ".output/server/index.mjs",
      args: "", // 传递给脚本的参数
      watch: true, // 开启监听文件变动重启
      ignore_watch: ["node_modules", "logs"], // 不用监听的文件
      exec_mode: "fork",// 自家主机window cluster_mode 模式下启动失败
      instances: "2", // max表示最大的 应用启动实例个数，仅在 cluster 模式有效 默认为 fork
      autorestart: true, // 默认为 true, 发生异常的情况下自动重启
      max_memory_restart: "1G",
      // error_file: './logs/app-err.log', // 错误日志文件
      // out_file: './logs/app-out.log', // 正常日志文件
      merge_logs: true, // 设置追加日志而不是新建日志
      log_date_format: "YYYY-MM-DD HH:mm:ss", // 指定日志文件的时间格式
      min_uptime: "60s", // 应用运行少于时间被认为是异常启动
      max_restarts: 30, // 最大异常重启次数
      restart_delay: 60, // 异常重启情况下，延时重启时间
      env: {
        // 环境参数，当前指定为开发环境
        NODE_ENV: "development",
        PORT: "3000", // 这里的端口自定义
      },
      env_production: {
        // 环境参数,当前指定为生产环境
        NODE_ENV: "production", //使用production模式 pm2 start ecosystem.config.js --env production
        PORT: "8007", // 这里的端口自定义
      },
      env_test: {
        // 环境参数,当前为测试环境
        NODE_ENV: "test",
      },
    },
  ],
};

```
<a name="cpf5b"></a>
## 启动服务
运行 pm2 start ecosystem.config.js 启动 pm2服务。
```shell
pm2 start ecosystem.config.js // 启动pm2服务
```
查看是否启动成功：如果服务是online说明启动成功，这时候再浏览器看一下自己项目，可以正常访问。
```shell
pm2 list
```
![pm2服务启动成功](https://cdn.nlark.com/yuque/0/2023/png/380273/1701938783807-ab7ef2fb-be29-4b66-bd8e-de2e2edb2e9d.png#averageHue=%23070707&clientId=u2df59f6a-3088-4&from=paste&height=294&id=u9912b48b&originHeight=294&originWidth=955&originalType=binary&ratio=1&rotation=0&showTitle=true&size=23456&status=done&style=none&taskId=u41986825-6663-400a-9b77-90c489292b2&title=pm2%E6%9C%8D%E5%8A%A1%E5%90%AF%E5%8A%A8%E6%88%90%E5%8A%9F&width=955 "pm2服务启动成功")
<a name="J1nkZ"></a>
## 设置自动重启
当服务器故障或者重启的时候，pm2服务能自动重启，就不需要每次服务器挂了再人为敲命令去重启pm2服务。
<a name="JMmXZ"></a>
### pm2-windows-startup
在linux下`pm2 startup`启动服务后，服务可以自动重启，而windows服务器就需要安装插件pm2-windows-startup：
<a name="EIN3I"></a>
#### 保存启动的服务列表状态
```powershell
pm2 save
```
<a name="ovJHN"></a>
#### 全局安装 pm2-windows-startup
```powershell
npm install pm2-windows-startup -g
```
<a name="uEeq9"></a>
#### 设置 pm2-startup
```powershell
pm2-startup install
```
以上步骤设置成功后，重启一下服务器，然后打开命令行，输入`pm2 list`看一下服务状态， 如果网站正常访问就说明设置成功。
<a name="ADyAc"></a>
## pm2常用命令
| 命令 | 用途 |
| --- | --- |
| pm2 list | 查看启动的服务列表 |
| pm2 restart nuxtjsDemo | 重启名为nuxtjsDemo的服务 |
| pm2 stop nuxtjsDemo | 终止名为nuxtjsDemo的服务 |
| pm2 delete nuxtjsDemo | 删除名为nuxtjsDemo的服务 |
| pm2 kill | 杀掉服务 |
| pm2 logs nuxtjsDemo | 查看名为nuxtjsDemo的服务日志 |


