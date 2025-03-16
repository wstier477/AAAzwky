# zwky_api 部署指南

本文档提供了将 zwky_api 项目部署到 PythonAnywhere 的详细步骤。

## 准备工作

1. 注册 PythonAnywhere 账户（如果还没有）
2. 确保您的代码已经上传到 GitHub 仓库

## 手动部署步骤

### 1. 登录到 PythonAnywhere

访问 [PythonAnywhere](https://www.pythonanywhere.com/) 并登录到您的账户。

### 2. 打开 Bash 控制台

在 PythonAnywhere 仪表板中，点击 "Consoles" 选项卡，然后点击 "Bash" 创建一个新的 Bash 控制台。

### 3. 克隆项目

由于项目在GitHub仓库的子目录中，需要执行以下步骤：

```bash
# 克隆整个仓库
git clone https://github.com/wstier477/AAAzwky.git
# 移动zwky_api目录到当前目录
mv AAAzwky/zwky_api .
# 删除不需要的仓库目录
rm -rf AAAzwky
# 进入项目目录
cd zwky_api
```

或者，您可以使用 PythonAnywhere 的文件上传功能上传您的代码。

### 4. 创建虚拟环境

```bash
cd zwky_api
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### 5. 配置 Django 设置

编辑 `zwky_api/pythonanywhere_settings.py` 文件，确认设置正确：

- 确认 `ALLOWED_HOSTS` 中包含 `wstier477.pythonanywhere.com`
- 如果使用 MySQL 数据库，取消注释并配置数据库设置
- 根据需要调整其他设置

### 6. 收集静态文件

```bash
python manage.py collectstatic --noinput --settings=zwky_api.pythonanywhere_settings
```

### 7. 应用数据库迁移

```bash
python manage.py migrate --settings=zwky_api.pythonanywhere_settings
```

### 8. 配置 Web 应用

1. 在 PythonAnywhere 仪表板中，点击 "Web" 选项卡
2. 点击 "Add a new web app"
3. 选择您的域名（`wstier477.pythonanywhere.com`）
4. 选择 "Manual configuration"
5. 选择 Python 版本（推荐 Python 3.8 或更高版本）

### 9. 配置 WSGI 文件

1. 在 Web 应用配置页面，找到 "Code" 部分
2. 点击 WSGI 配置文件链接（通常是 `/var/www/wstier477_pythonanywhere_com_wsgi.py`）
3. 删除文件中的所有内容，并粘贴 `pythonanywhere_wsgi.py` 的内容
4. 确认路径设置为 `/home/wstier477/zwky_api`
5. 保存文件

### 10. 配置虚拟环境路径

在 Web 应用配置页面的 "Virtualenv" 部分：

1. 输入您的虚拟环境路径：`/home/wstier477/zwky_api/venv`
2. 点击 "Virtualenv" 部分的保存按钮

### 11. 配置静态文件

在 Web 应用配置页面的 "Static files" 部分：

1. 添加一个新条目：
   - URL: `/static/`
   - Directory: `/home/wstier477/zwky_api/static/`
2. 如果需要媒体文件，再添加一个条目：
   - URL: `/media/`
   - Directory: `/home/wstier477/zwky_api/media/`
3. 点击 "Static files" 部分的保存按钮

### 12. 重新加载 Web 应用

点击 Web 应用配置页面顶部的 "Reload" 按钮。

## 使用自动部署脚本

为了简化部署过程，您可以使用提供的 `deploy_to_pythonanywhere.sh` 脚本：

1. 确认脚本中的设置正确：
   - USERNAME="wstier477"
   - GITHUB_REPO="https://github.com/wstier477/AAAzwky.git"
   - GITHUB_PROJECT_DIR="AAAzwky"
2. 在本地终端中运行脚本：

```bash
bash deploy_to_pythonanywhere.sh
```

注意：脚本执行后，您仍需要手动配置 Web 应用（步骤 8-12），但只需要在首次部署时进行。

## 故障排除

如果您遇到问题，请检查：

1. PythonAnywhere 的错误日志（在 Web 应用配置页面的 "Logs" 部分）
2. 确保所有路径都正确（特别是用户名和项目路径）
3. 确保虚拟环境中安装了所有必要的依赖
4. 确保 WSGI 文件配置正确

## 更新部署

要更新已部署的应用：

1. 将新代码推送到 GitHub 仓库
2. 登录到 PythonAnywhere Bash 控制台
3. 导航到项目目录并拉取更新：

```bash
cd zwky_api
git pull
source venv/bin/activate
pip install -r requirements.txt
python manage.py collectstatic --noinput --settings=zwky_api.pythonanywhere_settings
python manage.py migrate --settings=zwky_api.pythonanywhere_settings
```

4. 在 Web 应用配置页面点击 "Reload" 按钮

或者，再次运行自动部署脚本。 