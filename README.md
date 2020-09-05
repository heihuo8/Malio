# 请勿泄露源码给他人，当你让他人为你debug或进行其他操作时，切勿让他访问你网站的全部代码，尤其是不认识的人。泄露源码对你我都没好处。
安装宝塔面板
使用SSH登录你的VPS，根据你VPS的系统选择下面的安装命令
宝塔面板 Centos 一键安装脚本👇
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
宝塔面板 Ubuntu/Debian 一键安装脚本👇
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh
输入 y 回车安装
执行完毕后，会显示类似下面的宝塔面板的登录信息，请保存好此信息
Bt-Panel: http://xxx.xxx.xx.xx:8888/xxxxxxx
username: xxxxxxx
password: xxxxxxxx

安装LNMP
登录宝塔面板后，宝塔面板会提示你安装 PHP, MySQL, Nginx 等环境。
推荐使用 PHP 7.1、MySQL 5.6、Nginx任意版本。
需要一段时间才能安装完成，请等待完成。

配置PHP
打开 宝塔面版 > 软件商店 > 你安装的PHP > 设置，在禁用函数一栏删除 system、proc_open、proc_get_status、putenv、pcntl_signal。

安装Malio主题
打开 宝塔面版 > 网站 > 添加站点，输入你的域名，点击提交

在SSH里执行下面命令👇👇👇
下载SSPANEL+Malio的代码。如果你的这个网站文件夹包含原版的SSPANEL代码，请删除原版SSPANEL代码或者换个目录
cd /www/wwwroot/你的网站文件夹名
git clone -b malio https://gitlab.com/maxitio/malio-theme-for-sspanel.git tmp && mv tmp/.git . && rm -rf tmp && git reset --hard
然后Git会要求你输入Gitlab的账号密码
使用composer安装依赖
wget https://getcomposer.org/installer -O composer.phar
php composer.phar
php composer.phar install
用composer安装依赖时如果内存不够的话，可以使用 宝塔面版 > 首页 > Linux工具箱 > Swap/虚拟内存 增加虚拟内存
复制配置文件
cp config/.malio_config.example.php config/.malio_config.php
cp config/.config.example.php config/.config.php
cp config/.i18n.example.php config/.i18n.php

打开 宝塔面版 > 网站 > 你的网站
在 网站目录 里取消勾选 防跨站攻击，运行目录里面选择 /public，点击保存。
在 伪静态 中填入下面内容，然后保存
location / {
    try_files $uri /index.php$is_args$args;
}

在SSH里的网站目录下执行，给网站文件755权限
cd ../
chmod -R 755 你的文件夹名/
chown -R www:www 你的文件夹名/

数据库操作
打开 宝塔面版 > 数据库，选择添加数据库，数据库名字和用户名任意写，密码建议使用随机生成的密码，点击提交即可添加数据库
点击数据库名字旁边的 导入 > 本地上传，即可上传并导入sql文件到数据库
如果你是全新安装面板，请上传并导入网站目录下的 /sql/glzjin_all.sql、/sql/malio_all.sql
如果你是从其他主题迁移到Malio主题，请上传并导入网站目录下的 /sql/malio_all.sql、/sql/user_subscribe_log.sql、/sql/detect_ban_log.sql
如果没有导入 /sql/malio_all.sql 的话会导致无法注册新用户
将数据库user表里的全部用户的theme列改为malio，使用phpmyadmin执行这条sql语句👉 UPDATE user SET theme='malio'

编辑 config.php
将你的数据库名字，用户名和密码填入.config.php里，类似下面这样
$_ENV['db_database'] = 'sspanel';			//数据库名
$_ENV['db_username'] = 'sspanel';		//数据库用户名
$_ENV['db_password'] = 'sspanel_password';			//用户名对应的密码

其他操作
在SSH里的网站目录下执行 php xcat createAdmin，即可创建管理员用户。（如果你已经有了管理员用户就不用创建了）
在SSH里的网站目录下执行 php xcat initQQWry ，即可下载IP数据库（必须要执行这个）
下载通知频道里的 GeoLite2-City.mmdb 文件放入网站目录下的 /storage/ 目录下
为Crisp和Chatra的跨设备恢复会话功能生成UUID，在SSH里网站目录下执行👉 php xcat GenerateUUIDforAllUsers

配置定时任务
执行 crontab -e 命令，添加以下：
0 0 * * * php -n /www/wwwroot/你的文件夹名/xcat dailyjob
*/1 * * * * php /www/wwwroot/你的文件夹名/xcat checkjob
*/1 * * * * php /www/wwwroot/你的文件夹名/xcat syncnode

客户端下载
客户端的安装包需要自行下载到 /public/client-download/ 目录，安装包名字参考同目录下的 apps.txt 文件
另外在此目录下还提供了 download.sh 脚本，可自动下载部分客户端。

请仔细阅读 .malio_config.php 和 .config.php 的全部注释，进一步了解SSPANEL

本教程已经写得很详细了，安装还出问题的话请自行解决，或者联系我付费解决




将js授权文件上传到指定目录👉 /public/theme/malio/js/malio.js （别忘记重命名js文件名为malio.js）

然后编辑 .malio_config.php 里面的 malio_js_version 的值，随意更改这个值，只要跟之前的字符不一样即可

如果套了CDN的话记得刷新CDN缓存



怎么判断有没有授权成功
当你做完上面的操作后，如果手机访问用户中心页面侧边栏没有收起来的话就是授权没有成功。
===========================
其他问题
为什么移动的侧边栏收不起来

​

移动端侧边栏收不起来的原因是js授权没有生效，请检查你的授权：

访问的域名是否是已授权域名
检查浏览器获取的js文件是不是你在bot下载的那个文件
如果开了CDN，清除缓存试试
在.config.php里改js版本号，再清空CDN缓存试试
怎么去掉客户端节点列表里面的“单端口”字样

编辑 app/Utils/URL.php 和 app/Utils/Tools.php，在文件里搜 单 字即可找到

Tools.php 576行从 'name' => ($node_name . ' - ' . $node_port . ' 单端口'), 改成 'name' => $node_name,

URL.php 556行从 $server['remarks'] = $node->name . ' - 单多' . $server['port'] . '端口'; 改成 $server['remarks'] = $node->name;

URL.php 616行从 $server['remarks'] = $node->name . ' - 单多' . $node_muport->server . '端口'; 改成 $server['remarks'] = $node->name;

URL.php 626行从 $server['remarks'] = $node->name . ' - 单多' . $node_tmp['port'] . '端口'; 改成 $server['remarks'] = $node->name;

删除URL.php 715行 $node_name .= ' - ' . $mu_port . ' 单端口';

节点列表里面的标题要怎么改？

 可以编辑 /config/.i18n.php 里面的 $I18N['node-class-name-map'] 的内容，记得也改一下英文的

商品添加后商城不显示商品

编辑/config/.malio_config.php 里面的$Malio_Config['plan_shop_id'] = array

依次对应后台设置好的商品ID

不能注册

请在数据库执行这两条SQL语句，分开执行。

ALTER TABLE user ADD uid text; 
ALTER TABLE user ADD phone bigint(20) AFTER email;
安装完旧用户不能登录

 .config.php里面的 $_ENV['pwdMethod']的值是切换到sha256或MD5

签到、订阅、工单系统、充值点击没反应

检查授权文件名称、清除malio_config.php内缓存值
=================对接支付
对接 Stripe 支付接口
目前 Stripe 支持支付宝和微信支付。

在 Stripe 管理平台的侧边栏里找到 开发者 -> API密钥，获取到密钥（格式为sk_xxxxxxxxxxxxxx），填入 .config.php 里的 stripe_key。

然后打开 开发者 -> Webhook，选择添加端点，端点 URL 写 https://你的域名/payment/notify (如果你用的是Malio聚合支付接口要写 https://你的域名/payment/notify/stripe)，要发送的事件选择 source.chargeable，最后点击添加端点。

然后点击进去看刚刚添加的 Webhook 详情，找到密钥签名（格式为whsec_xxxxxxxxxxxx），填入 .config.php 里的 stripe_webhook_endpoint_secret。

在 .malio_config.php 里可以设置 Stripe 支付接口可充值的最低金额。




