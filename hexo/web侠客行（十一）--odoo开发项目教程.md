---
title: web侠客行（十一）--odoo开发项目教程
date: 2019-05-19 10:02:12
tags: 
    - 企业管理
    - odoo
categories: 
    - 教程
    - 网站建设
---

# 1.odoo安装

## 基础准备
```
# 升级系统
sudo apt-get update
sudo apt-get dist-upgrade

# 安装 PostgreSQL
sudo apt-get install postgresql -y
# 进入数据库
sudo -u postgres psql
# 为当前用户添加创建数据库权限
sudo su - postgres
createuser --createdb --username postgres --pwprompt alan
```

## 快速安装
```
# 文档推荐版本wkhtmltopdf 0.12.1
# https://github.com/wkhtmltopdf/wkhtmltopdf/releases/tag/0.12.1
sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.1/wkhtmltox-0.12.1_linux-trusty-amd64.deb
sudo dpkg -i wkhtmltox-0.12.1_linux-trusty-amd64.deb 
# 安装缺失的包
sudo apt-get install -f
sudo dpkg -i wkhtmltox-0.12.1_linux-trusty-amd64.deb 

### Odoo 仓库安装 ###
# 使用 root 权限
sudo su - 
sudo wget -O - https://nightly.odoo.com/odoo.key | apt-key add -
echo "deb http://nightly.odoo.com/11.0/nightly/deb/ ./" >> /etc/apt/sources.list.d/odoo.list
apt-get update && apt-get install odoo
# 如果报错需要依赖，则执行
apt --fix-broken install
# 启动服务
systemctl start odoo

### <a href="https://nightly.odoo.com/11.0/nightly/deb/" target="_blank" rel="noopener" data-mce-href="https://nightly.odoo.com/11.0/nightly/deb/">Odoo deb 包</a>安装 ###
sudo wget https://nightly.odoo.com/11.0/nightly/deb/odoo_11.0.latest_all.deb

# 方法一
sudo apt-get install gdebi-core
sudo gdebi odoo_11.0.latest_all.deb 

# 方法二
dpkg -i odoo_11.0.latest_all.deb 
sudo apt-get install -f
dpkg -i odoo_11.0.latest_all.deb 

# 启动服务
systemctl start odoo
```

## 源码安装
```
# 安装PostgreSQL
sudo apt-get install postgresql -y
 
# NodeJS
sudo apt-get install -y nodejs npm
sudo npm install -g less
 
# Virtualenvwrapper配置虚拟环境
sudo apt install virtualenvwrapper
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
sudo apt install build-essential python3-dev libxslt-dev libzip-dev libldap2-dev libsasl2-dev
mkvirtualenv -p /usr/bin/python3 odoo-venv
# 退出环境
deactivate
# 重新进入环境
workon odoo-venv
## 源码下载（官方仓库） ##
# https://nightly.odoo.com/11.0/nightly/src/
wget https://nightly.odoo.com/11.0/nightly/src/odoo_11.0.latest.tar.gz
tar -zxf odoo_11.0.latest.tar.g
mv odoo-11.0.post20180714/ odoo11
cd odoo11
# 安装依赖
pip install -r requirements.txt
# 初始化安装
python setup.py install
 
## Git 下载源代码 ##
git clone https://www.github.com/odoo/odoo --depth 1 --branch 11.0 --single-branch odoo11
```

## odoo依赖
```
sudo apt-get install python-cups python-dateutil python-decorator python-docutils python-feedparser \
     python-gdata python-geoip python-gevent python-imaging python-jinja2 python-ldap \
     python-libxslt1 python-lxml python-mako python-mock python-openid python-passlib \
     python-psutil python-psycopg2 python-pybabel python-pychart python-pydot python-pyparsing \
     python-pypdf python-reportlab python-requests python-simplejson python-tz python-unicodecsv \
     python-unittest2 python-vatnumber python-vobject \
     python-werkzeug python-xlwt python-yaml wkhtmltopdf \
     python-pip python-dev libevent-dev gcc libxml2-dev libxslt-dev node-less geoip-database-contrib
```

## 补充安装方法
```
# 更新 apt 源
apt-get update
# 安装Python编译依赖
sudo apt-get install build-essential python-dev python-setuptools build-essential -y
# 安装 pip
sudo easy_install pip
# 安装lxml、ldap、pillow 依赖
sudo apt-get install -y zlib1g-dev libxslt1-dev libxml2-dev libsasl2-dev libldap2-dev libssl-dev libtiff5-dev libjpeg8-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev
# 使用virtualenv创建虚拟环境
sudo pip install virtualenv
mkdir odoo11
cd odoo11
virtualenv odoo-venv
# 安装 PostgrSQL 数据库
sudo apt-get install postgresql -y
# 创建用户
sudo -u postgres psql
create user "odoo11" with password 'odoo11' createdb;
# \q 退出
# 安装 NodeJS
sudo apt-get install -y nodejs npm
# 安装 Less
sudo npm install -g less less-plugin-clean-css
# 下载 Odoo11
git clone https://www.github.com/odoo/odoo --depth 1 --branch 11.0 --single-branch
# 启动虚拟环境
source odoo-venv/bin/activate
# 安装 Odoo Python 依赖，可使用豆瓣源加速
pip install -r requirements.txt -i https://pypi.douban.com/simple
# odoo配置文件
cp debian/odoo.conf ./
vi odoo.conf

[options]
; This is the password that allows database operations:
; admin_passwd = admin
db_host = 127.0.0.1
db_port = False
db_user = odoo11
db_password = odoo11
;addons_path = /usr/lib/python3/dist-packages/odoo/addons

启动服务
./odoo-bin -c odoo.conf
```


注：

在安装 psutil 的过程中由于系统存在多个 Python 版本会出现报错psutil/_psutil_common.c:9:20: fatal error: Python.h: No such file or directory

此时应安装Python指定版本的 dev，如：

sudo apt-get install libpython3.5-dev

sudo apt install -y nodejs-legacy

遇到服务后台启动占用端口可予以停用

sudo lsof -t -i tcp:8069 | xargs kill -9

# 什么是 odoo

根据预期的用例，有多种方法可以安装Odoo，或者根本不安装它。

本文档试图描述大多数安装选项。

线上
在生产中使用Odoo或尝试它的最简单方法。
打包安装程序
适用于测试Odoo，开发模块，可用于长期生产，并可进行额外的部署和维护工作。
源安装
提供更大的灵活性：例如，允许在同一系统上运行多个Odoo版本。适合开发模块，可用作生产部署的基础。
搬运工人
如果您通常使用docker进行开发或部署，则可以使用官方 docker基础映像。
版本
Odoo 有两种不同版本：社区版和企业版。可以在我们的SaaS上使用企业版，访问代码仅限于企业客户和合作伙伴。社区版本可供所有人免费使用。

如果您已使用社区版并希望升级到Enterprise，请参阅从社区到企业（源安装除外）。

线上
演示
为了简单快速了解Odoo，可以使用演示实例。它们是共享实例，只能活几个小时，可用于浏览并尝试不做任何承诺。

演示实例不需要本地安装，只需要Web浏览器。

SaaS的
由Odoo SA开始，完全管理和迁移的琐碎，Odoo的SaaS 提供私人实例并免费开始。它可用于发现和测试Odoo并进行非代码自定义（即与自定义模块或Odoo Apps Store不兼容），而无需在本地安装。

可用于测试Odoo和长期生产使用。

与演示实例一样，SaaS实例不需要本地安装，Web浏览器就足够了。

打包安装程序
Odoo为社区和企业版本提供Windows的打包安装程序，基于deb的发行版（Debian，Ubuntu，...）和基于RPM的发行版（Fedora，CentOS，RHEL，...）。

这些包自动设置所有依赖项（对于社区版本），但可能难以保持最新。

我们的夜间服务器上提供了具有所有相关依赖性要求的官方社区包。Communtiy和Enterprise软件包都可以从我们的下载页面下载（您必须以付费客户或合作伙伴的身份登录才能下载企业软件包）。

视窗
从我们的夜间服务器（仅限社区）下载安装程序，或从下载页面（任何版本）下载 Windows安装程序
运行下载的文件

警告
在Windows 8上，您可能会看到一个标题为“Windows保护您的PC”的警告。单击更多信息，然后 单击运行

接受UAC提示
完成各种安装步骤
Odoo将在安装结束时自动启动。

Linux的
于Debian / Ubuntu
Odoo 12.0'deb'软件包目前支持Debian Stretch，Ubuntu 18.04或更高版本。

准备
Odoo需要PostgreSQL服务器才能正常运行。Odoo'deb'软件包的默认配置是在与Odoo实例相同的主机上使用PostgreSQL服务器。以root身份执行以下命令以安装PostgreSQL服务器：

＃ apt-get install postgresql -y
要打印PDF报告，您必须自己安装wkhtmltopdf：Debian存储库中提供的wkhtmltopdf版本不支持页眉和页脚，因此不会将其用作直接依赖项。推荐版本为0.12.5，可在 wkhtmltopdf下载页面的归档部分中找到。以前推荐的0.12.1版本是一个不错的选择。有关各种版本及其各自怪癖的更多详细信息，请参阅我们的维基。

知识库
Odoo SA提供了一个可以与Debian和Ubuntu发行版一起使用的存储库。它可以通过以root身份执行以下命令来安装Odoo Community Edition：

＃ wget的-O - https://nightly.odoo.com/odoo.key | 易键添加-
＃ 回声 “的deb http://nightly.odoo.com/12.0/nightly/deb/ ./” >> /etc/apt/sources.list.d/odoo.list
 ＃ apt-get的更新&& apt-get install odoo
然后，您可以使用常用apt-get upgrade命令使您的安装保持最新。

目前，企业版没有存储库。

Deb包
可以在此处下载“deb”包，而不是如上所述使用存储库：

社区版：每晚
企业版下载
然后你可以使用gdebi：

＃ gdebi <path_to_installation_package>
或者dpkg：

＃ dpkg -i来<path_to_installation_package> ＃这可能失败，缺少的依赖关系
＃易于得到安装-f ＃应该安装缺少的依赖关系
＃ dpkg -i来<path_to_installation_package>
这将安装Odoo作为服务，创建必要的PostgreSQL用户并自动启动服务器。

警告
Debian Stretch和Ubuntu 18.04中不存在python3-xlwt Debian软件包。导出为xls格式需要此python模块。

如果需要此功能，可以手动安装。一种方法，就是使用像这样的pip3：

$ sudo pip3 install xlwt
警告
Debian 9和Ubuntu没有提供python模块num2words的包。Odoo不会呈现文本金额，这可能会导致“l10n_mx_edi”模块出现问题。

如果您需要此功能，可以像这样安装python模块：

$ sudo pip3安装num2words
Fedora的
Odoo 12.0'rpm'软件包支持Fedora 26.截至2017年，CentOS没有Odoo 12.0的最低Python要求（3.5）。

准备
Odoo需要PostgreSQL服务器才能正常运行。假设'sudo'命令可用且配置正确，请运行以下命令：

$ sudo dnf install -y postgresql-server
 $ sudo postgresql-setup --initdb --unit postgresql
 $ sudo systemctl enable postgresql
 $ sudo systemctl start postgresql
要打印PDF报告，您必须自己安装wkhtmltopdf：Debian存储库中提供的wkhtmltopdf版本不支持页眉和页脚，因此不会将其用作直接依赖项。推荐版本为0.12.5，可在 wkhtmltopdf下载页面的归档部分中找到。以前推荐的0.12.1版本是一个不错的选择。有关各种版本及其各自怪癖的更多详细信息，请参阅我们的维基。

知识库
Odoo SA提供了一个可以与Fedora distibutions一起使用的存储库。它可以通过执行以下命令来安装Odoo Community Edition：

$ sudo dnf config-manager --add-repo = https://nightly.odoo.com/12.0/nightly/rpm/odoo.repo
 $ sudo dnf install -y odoo
 $ sudo systemctl enable odoo
 $ sudo systemctl start odoo
RPM包
可以在此处下载'rpm'包而不是如上所述使用存储库：

社区版：每晚
企业版下载
下载后，可以使用'dnf'软件包管理器安装软件包：

$ sudo dnf localinstall odoo_12.0.latest.noarch.rpm
 $ sudo systemctl enable odoo
 $ sudo systemctl start odoo
源安装
源“安装”实际上是关于不安装Odoo，而是直接从源代码运行它。

这对于模块开发人员来说更方便，因为Odoo源比使用打包安装更容易访问（用于获取信息或构建此文档并使其可脱机使用）。

它还使打包和停止Odoo比打包安装设置的服务更灵活和明确，并允许使用命令行参数覆盖设置，而无需编辑配置文件。

最后，它提供了对系统设置的更大控制，并允许更容易地并排（并运行）多个版本的Odoo。

准备
源安装需要手动安装依赖项：

Python 3.5+。

在Linux和OS X上，如果没有默认安装，请使用包管理器

在某些系统上，python命令是指Python 2（过时）或Python 3（支持）。确保您使用的是正确的版本，并且您的版本python3中存在 别名PATH

在Windows上，使用官方的Python 3安装程序。

警告
在安装过程中选择“将python.exe添加到路径”，然后重新启动以确保PATH更新

如果已经安装了Python，请确保它是3.5或更高版本，以前的版本与Odoo不兼容。

PostgreSQL，使用本地数据库

安装完成后，您需要创建一个postgres用户：默认情况下，唯一的用户是postgres，并且Odoo禁止连接为postgres。

在Linux上，使用您的发行版包，然后创建一个名为您的登录名的postgres用户：

$ sudo su  -  postgres -c “createuser -s $ USER”
因为角色登录与unix登录相同，所以unix套接字可以在没有密码的情况下使用。

在OS X上，postgres.app是最简单的入门方式，然后在Linux上创建一个postgres用户
在Windows上，然后使用PostgreSQL for windows

将PostgreSQL的bin目录（默认值 :)添加C:\Program Files\PostgreSQL\9.4\bin到您的PATH
使用pg admin gui创建一个带密码的postgres用户：打开pgAdminIII，双击服务器创建连接，选择 编辑‣新建对象‣新登录角色，在角色名称字段中输入用户名（例如odoo），然后打开在“ 定义”选项卡中输入密码（例如odoo），然后单击“ 确定”。

必须使用-w和-r选项或 配置文件将用户和密码传递给Odoo

requirements.txt文件中列出的Python依赖项。

在Linux上，python依赖项可以使用系统的包管理器或使用pip进行安装。

对于使用本机代码的库（Pillow，lxml，greenlet，gevent，psycopg2，ldap），在pip能够自己安装依赖项之前，可能需要安装开发工具和本机依赖项。这些在可-dev或-devel为Python，Postgres的，libxml2的，的libxslt，libevent的，libsasl2的libldap2和包。然后可以安装Python依赖项：

$ pip3 install -r requirements.txt
在OS X上，您需要安装命令行工具（xcode-select --install）然后下载并安装您选择的软件包管理器（自制软件，macports）以安装非Python依赖项。然后可以使用pip来安装Python依赖项，就像在Linux上一样：

$ pip3 install -r requirements.txt
在Windows上，您需要手动安装一些依赖项，调整requirements.txt文件，然后运行pip来安装重新创建的依赖项。

安装psycopg使用此处安装 http://www.stickpeople.com/projects/python/win-psycopg/

然后使用pip从cmd.exe提示符下使用以下命令安装依赖项（替换\YourOdooPath为您下载Odoo的实际路径）：

C：\>  cd \ YourOdooPath
 C：\ YourOdooPath> C：\ Python35 \ Scripts \ pip.exe install -r requirements.txt
RTLCSS通过的NodeJS

对于具有从右到左界面的语言（例如阿拉伯语或希伯来语），rtlcss需要包。

在Linux上，使用您的发行版的软件包管理器来安装nodejs和npm。安装npm后，使用它来安装rtlcss：

$ sudo npm install -g rtlcss
在OS X上，通过首选软件包管理器（自制软件， macports）安装nodejs 然后安装less：

$ sudo npm install -g rtlcss
在Windows上，安装nodejs，重新启动（更新PATH）并安装rtlcss：

C：\> npm install -g rtlcss
然后，需要编辑系统环境的变量 PATH并添加所在的文件夹rtlcss.cmd。典型：

C：\用户\ <用户> \应用程序数据\漫游\ NPM \
获取源
有两种方法可以获得Odoo源代码：zip或git。

Odoo zip可以从我们的夜间服务器或我们的下载 页面下载，然后需要解压缩zip文件才能使用其内容
git允许更简单的更新，并更容易在不同版本的Odoo之间切换。它还简化了维护非模块补丁和贡献。git的主要缺点是它比tarball大得多，因为它包含了Odoo项目的整个历史。
社区版
社区版的git存储库是https://github.com/odoo/odoo.git。

下载它需要一个git客户端 （可以通过你在linux上的发行版获得），并且可以使用以下命令执行：

$ git clone https://github.com/odoo/odoo.git
企业版
如果您有权访问Enterprise存储库（ 如果您希望获得访问权限，请参阅版本），您可以使用此命令来获取插件：

$ git clone https://github.com/odoo/enterprise.git
Enterprise git存储库不包含完整的Odoo源代码。它只是一组额外的附加组件。主服务器代码位于社区版本中。运行Enterprise版本实际上意味着从社区版本运行服务器，并将addons-path选项设置为具有Enterprise版本的文件夹。

您需要克隆社区和企业存储库才能安装有效的Odoo

运行Odoo
一旦设置了所有依赖项，就可以通过运行来启动Odoo odoo-bin。

对于企业版，您必须enterprise 在启动服务器时指定addons文件夹。您可以通过enterprise在addons-path参数中提供文件夹的路径来实现。请注意，该enterprise文件夹必须addons位于列表中的默认文件夹之前 才能正确加载插件。

配置可以通过提供 命令行参数或通过 配置文件。

常见的必要配置是：

PostgreSQL主机，端口，用户和密码。

Odoo没有超出 psycopg2默认值的默认值：通过端口5432上的UNIX套接字与当前用户连接，没有密码。默认情况下，这应该适用于Linux和OS X，但它不适用于Windows，因为它不支持UNIX套接字。

超出默认值的自定义插件路径，以加载您自己的模块
在Windows下，执行odoo的典型方法是：

C：\ YourOdooPath> python3 odoo-bin -w odoo -r odoo --addons-path = addons，.. / mymodules --db-filter = mydb $
其中odoo，odoo是postgresql登录名和密码， ../mymodules带有附加插件的目录以及mydb在localhost上提供的默认数据库：8069

在Unix下，执行odoo的典型方法是：

$ ./odoo-bin --addons-path = addons，.. / mymodules --db-filter = mydb $
../mymodules带有其他插件的目录在哪里，以及mydb在localhost上提供的默认数据库：8069

VIRTUALENV
Virtualenv是一个创建Python隔离环境的工具，因为有时候最好不要将你的发布python模块包与全局安装的python模块与pip混合使用。

本节将解释如何在这样的隔离Python环境中运行Odoo。

在这里，我们将使用virtualenvwrapper，它是一组shell脚本，使virtualenv的使用更容易。

以下示例基于Debian 9发行版，但可以在virtualenvwrapper和virtualenv能够运行的任何平台上进行调整。

本节假定您已从zip文件或git存储库获取Odoo源，如上所述。这同样适用于postgresql安装和配置。

安装virtualenvwrapper
$ sudo apt install virtualenvwrapper
 $  source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
这将安装virtualenvwrapper并立即激活它。现在，让我们安装构建Odoo依赖项所需的工具（如果需要）：

$ sudo apt install build-essential python3-dev libxslt-dev libzip-dev libldap2-dev libsasl2-dev
创建一个孤立的环境
现在我们可以像这样为Odoo创建一个虚拟环境：

$ mkvirtualenv -p / usr / bin / python3 odoo-venv
使用此命令，我们要求一个名为“odoo-env”的隔离Python3环境。如果命令按预期工作，则shell现在正在使用此环境。您的提示应该已更改，以提醒您正在使用隔离环境。您可以使用以下命令进行验证：

$ python3
此命令应显示位于隔离环境目录中的Python解释器的路径。

现在让我们安装Odoo所需的python包：

$  cd your_odoo_sources_path
 $ pip install -r requirements.txt
过了一会儿，你应该准备好从命令行运行odoo，如上所述。

如果要离开虚拟环境，只需发出以下命令：

$ deactivate
每当您想再次使用'odoo-venv'环境时：

$ workon odoo-venv
搬运工人
有关如何将Odoo与Docker一起使用的完整文档可以在官方的Odoo docker 图像页面上找到。
