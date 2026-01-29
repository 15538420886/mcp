# EdgeOne Pages MCP

一个用于将 HTML 内容、文件夹或全栈项目部署到 EdgeOne Pages 并获取公开访问链接的 MCP 服务。

<a href="https://glama.ai/mcp/servers/@TencentEdgeOne/edgeone-pages-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@TencentEdgeOne/edgeone-pages-mcp/badge" alt="EdgeOne Pages MCP server" />
</a>

## 演示

### 部署 HTML

![](https://cdnstatic.tencentcs.com/edgeone/pages/assets/U_GpJ-1746519327306.gif)

### 部署文件夹

![](https://cdnstatic.tencentcs.com/edgeone/pages/assets/kR_Kk-1746519251292.gif)

## 环境要求

- Node.js 18 或更高版本

## 在腾讯云上部署

本 MCP 服务支持多种部署方式，您可以根据需求选择最适合的方案。

### 方式一：使用自托管版本部署到 EdgeOne Pages（推荐）

这是最简单快捷的部署方式，适合需要 HTTP 访问的场景。

#### 步骤 1：一键部署到 EdgeOne Pages

1. 访问 [自托管版本仓库](https://github.com/TencentEdgeOne/self-hosted-pages-mcp)
2. 点击"一键部署"按钮，将服务部署到 EdgeOne Pages
3. 按照提示完成部署配置

#### 步骤 2：配置 KV 存储

部署完成后，需要配置 KV 存储用于存储网站文件：

1. 进入 EdgeOne Pages 控制台
2. 找到 KV Storage 配置
3. 按照 [KV 存储设置指南](https://pages.edgeone.ai/document/kv-storage) 完成配置

#### 步骤 3：绑定自定义域名（可选）

1. 在项目设置中添加自定义域名
2. 配置 DNS CNAME 记录
3. 等待域名验证通过
4. 参考 [自定义域名绑定指南](https://pages.edgeone.ai/document/custom-domain)

#### 步骤 4：配置 MCP 客户端

部署完成后，在 MCP 配置文件中添加：

```json
{
  "mcpServers": {
    "edgeone-pages": {
      "url": "https://your-custom-domain.com/mcp-server"
    }
  }
}
```

**优势：**
- ✅ 零服务器运维成本
- ✅ 自动扩展和高可用
- ✅ 全球边缘加速
- ✅ 支持自定义域名和 HTTPS

### 方式二：在腾讯云 CVM 上部署

适合需要完全控制服务器环境或需要 stdio 传输方式的场景。

#### 步骤 1：准备 CVM 实例

1. 登录 [腾讯云控制台](https://console.cloud.tencent.com/)
2. 创建或选择一台 CVM 实例
3. 推荐配置：
   - **操作系统**：Ubuntu 20.04 LTS 或 CentOS 7+
   - **CPU**：2 核或以上
   - **内存**：4GB 或以上
   - **网络**：配置公网 IP 或弹性公网 IP

#### 步骤 2：安装 Node.js 环境

**Ubuntu/Debian：**
```bash
# 更新系统包
sudo apt update

# 安装 Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证安装
node --version
npm --version
```

**CentOS/RHEL：**
```bash
# 安装 Node.js 18.x
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs

# 验证安装
node --version
npm --version
```

#### 步骤 3：部署 MCP 服务

```bash
# 克隆项目（或上传项目文件）
git clone https://github.com/TencentEdgeOne/edgeone-pages-mcp.git
cd edgeone-pages-mcp

# 安装依赖
npm install

# 构建项目
npm run build

# 配置环境变量
cat > .env << EOF
EDGEONE_PAGES_API_TOKEN=your-api-token-here
EDGEONE_PAGES_PROJECT_NAME=your-project-name
EOF
```

#### 步骤 4：配置进程管理（使用 PM2）

```bash
# 安装 PM2
sudo npm install -g pm2

# 启动服务
pm2 start dist/index.js --name edgeone-pages-mcp

# 设置开机自启
pm2 startup
pm2 save

# 查看服务状态
pm2 status
pm2 logs edgeone-pages-mcp
```

#### 步骤 5：配置防火墙

```bash
# Ubuntu/Debian (UFW)
sudo ufw allow 22/tcp    # SSH
# 如果使用 HTTP 传输，还需要开放相应端口
# sudo ufw allow 8080/tcp

# CentOS (firewalld)
sudo firewall-cmd --permanent --add-service=ssh
# sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

#### 步骤 6：配置 MCP 客户端

如果使用 stdio 传输（本地或 SSH）：

```json
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "command": "node",
      "args": ["/path/to/edgeone-pages-mcp/dist/index.js"],
      "env": {
        "EDGEONE_PAGES_API_TOKEN": "your-api-token",
        "EDGEONE_PAGES_PROJECT_NAME": "your-project-name"
      }
    }
  }
}
```

**优势：**
- ✅ 完全控制服务器环境
- ✅ 支持 stdio 传输方式
- ✅ 适合企业内网环境

### 方式三：使用腾讯云云开发平台部署

适合需要快速部署且希望使用腾讯云托管服务的场景。

#### 步骤 1：创建云开发环境

1. 访问 [腾讯云云开发控制台](https://console.cloud.tencent.com/tcb)
2. 创建新环境或选择现有环境
3. 选择 Node.js 18+ 运行环境

#### 步骤 2：部署代码

1. 在云开发控制台选择"云函数"
2. 创建新函数或上传代码
3. 配置函数入口为 `index.js`
4. 设置环境变量：
   - `EDGEONE_PAGES_API_TOKEN`
   - `EDGEONE_PAGES_PROJECT_NAME`

#### 步骤 3：配置 HTTP 触发器

1. 在函数配置中添加 HTTP 触发器
2. 配置访问路径和认证方式
3. 获取触发器 URL

#### 步骤 4：配置 MCP 客户端

```json
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "url": "https://your-function-url.tencentcloudapi.com"
    }
  }
}
```

**优势：**
- ✅ 无需管理服务器
- ✅ 自动扩缩容
- ✅ 集成腾讯云服务

### 安全建议

无论使用哪种部署方式，都建议：

1. **保护 API Token**：
   - 使用环境变量存储敏感信息
   - 不要将 `.env` 文件提交到代码仓库
   - 定期轮换 API Token

2. **网络安全**：
   - 使用 HTTPS 访问服务
   - 配置防火墙规则限制访问来源
   - 使用 VPN 或内网访问（如适用）

3. **监控和日志**：
   - 配置日志收集和监控
   - 设置告警规则
   - 定期检查服务状态

### 故障排查

**问题：服务无法启动**
- 检查 Node.js 版本是否符合要求（18+）
- 检查依赖是否正确安装：`npm install`
- 查看日志：`pm2 logs` 或查看云函数日志

**问题：API Token 无效**
- 验证 Token 是否正确配置
- 检查 Token 是否过期
- 参考 [API Token 文档](https://edgeone.ai/document/177158578324279296)

**问题：部署失败**
- 检查网络连接
- 验证 API Token 权限
- 查看详细错误日志

## 配置 MCP

### stdio MCP 服务器

完整功能的 MCP 服务，支持 `deploy_folder` 工具，用于部署全栈项目。

```jsonc
// 腾讯云国际站（默认）
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "timeout": 600,
      "command": "npx",
      "args": ["edgeone-pages-mcp-fullstack"]
    }
  }
}

// 腾讯云中国站
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "timeout": 600,
      "command": "npx",
      "args": ["edgeone-pages-mcp-fullstack", "--region", "china"]
    }
  }
}
```

以下 MCP Server 即将废弃：

支持 `deploy_html` 和 `deploy_folder_or_zip` 两种工具。

```jsonc
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "command": "npx",
      "args": ["edgeone-pages-mcp"],
      "env": {
        // 可选配置。
        // 如果需要将文件夹或 zip 文件部署到 EdgeOne Pages 项目
        // 请提供您的 EdgeOne Pages API 令牌。
        // 获取 API 令牌的方法：
        // https://edgeone.ai/document/177158578324279296
        "EDGEONE_PAGES_API_TOKEN": "",
        // 可选配置。留空将创建新的 EdgeOne Pages 项目。
        // 提供项目名称可更新现有项目。
        "EDGEONE_PAGES_PROJECT_NAME": ""
      }
    }
  }
}
```

### 流式 HTTP MCP 服务器

适用于支持 HTTP 流式传输的 MCP 客户端，仅支持 `deploy_html` 工具。

```json
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "url": "https://mcp-on-edge.edgeone.site/mcp-server"
    }
  }
}
```

## 工具详解

### deploy_html 工具

#### 架构设计

![EdgeOne Pages MCP 架构图](./assets/architecture.svg)

架构图展示了 `deploy_html` 工具的完整工作流程：

1. 大语言模型生成 HTML 内容
2. 内容发送到 EdgeOne Pages MCP 服务器
3. MCP 服务器将内容部署到 EdgeOne Pages 边缘函数
4. 内容存储在 EdgeOne KV 存储中，实现快速边缘访问
5. MCP 服务器返回公开访问链接
6. 用户可通过浏览器访问部署的内容，享受快速的边缘分发服务

#### 实现原理

本工具与 EdgeOne Pages Functions 集成，用于部署静态 HTML 内容：

1. **EdgeOne Pages Functions** - 一个无服务器计算平台，支持在边缘执行 JavaScript/TypeScript 代码

2. **核心实现细节**：

   - 使用 EdgeOne Pages KV 存储来保存和提供 HTML 内容
   - 自动为每次部署生成公开访问链接
   - 提供完善的 API 错误处理和错误信息反馈

3. **工作原理**：
   - MCP 服务器通过 `deploy_html` 工具接收 HTML 内容
   - 连接 EdgeOne Pages API 获取基础 URL
   - 使用 EdgeOne Pages KV API 部署 HTML 内容
   - 返回可立即访问的公开链接

更多信息请参考 [EdgeOne Pages Functions 文档](https://edgeone.ai/document/162227908259442688) 和 [EdgeOne Pages KV 存储指南](https://edgeone.ai/document/162227803822321664)。

源码开源，可以自部署，绑定自定义域名使用：https://github.com/TencentEdgeOne/self-hosted-pages-mcp

### deploy_folder 工具

此工具支持将完整项目部署到 EdgeOne Pages：

- 支持静态网站项目的完整部署
- 支持全栈应用的部署
- 可选择更新现有项目或创建新项目

### 自定义域名配置

EdgeOne Pages MCP 支持自定义域名部署。在 EdgeOne Pages 控制台配置自定义域名后，部署将自动使用您的自定义域名，而不是默认的临时链接。

#### 自定义域名的优势

- **品牌展示**：使用您自己的品牌域名，提升专业形象
- **SEO 优化**：有利于搜索引擎索引和排名
- **更好的控制**：完全控制域名的 DNS 记录
- **灵活性**：可配置多个自定义域名，方便为不同市场或目的进行管理

#### 如何配置自定义域名

**第一步：在控制台添加自定义域名**

1. 部署项目后，打开 [EdgeOne Pages 控制台](https://console.tencentcloud.com/edgeone/pages)
2. 进入项目设置页面
3. 找到"域名管理"模块
4. 点击"添加自定义域名"按钮
5. 输入根域名（如 `example.com`）或子域名（如 `www.example.com`）

**第二步：配置 DNS CNAME 记录**

1. 控制台会显示一个 CNAME 记录值（如 `a4285573.xxx.example.com.dns.edgeone.site.`）
2. 前往您的域名注册商的 DNS 管理页面
3. 添加一条 CNAME 记录，填写：
   - **类型**：CNAME
   - **主机记录**：您的子域名（如 `www`）或 `@` 表示主域名
   - **记录值**：EdgeOne Pages 提供的 CNAME 值
   - **TTL**：600 秒（建议设置较低值以加快生效）
4. 保存 DNS 记录

**第三步：验证域名**

1. 返回 EdgeOne Pages 控制台
2. 点击"验证"按钮或等待系统自动检测 DNS 解析
3. DNS 记录通常在几分钟内生效，最长可达 48 小时
4. 验证成功后，域名状态将变为"通过"

**第四步：配置 HTTPS（推荐）**

1. 域名验证通过后，进入"HTTPS 配置"部分
2. 申请免费 SSL 证书或使用托管 SSL 证书
3. 证书将自动为您的自定义域名配置

**第五步：使用自定义域名部署**

完成上述步骤后，当您使用 `deploy_folder_or_zip` 部署时：

- 如果您的项目已配置并验证通过（状态为"通过"）的自定义域名，部署将自动使用您的自定义域名
- 返回的 URL 将是 `https://your-custom-domain.com`，而不是临时链接
- MCP 工具中无需额外配置

#### 重要注意事项

- **备案要求**：如果项目的加速区域为"中国大陆可用区"或"全球可用区（含中国大陆）"，添加域名前需先在工信部完成备案
- **域名状态**：只有状态为"通过"的域名才会在部署中自动使用
- **多个域名**：如果配置了多个自定义域名，将使用第一个状态为"通过"的域名
- **控制台链接**：部署后，您可以通过部署结果中提供的项目控制台链接来管理域名

#### 使用自定义域名的部署结果示例

```json
{
  "type": "custom",
  "url": "https://www.example.com",
  "projectId": "your-project-id",
  "projectName": "your-project-name",
  "consoleUrl": "https://console.tencentcloud.com/edgeone/pages/project/your-project-id/index"
}
```

更多信息请参考：
- [自定义域名文档](https://edgeone.ai/document/175201436224495616)
- [DNS CNAME 配置指南](https://edgeone.ai/document/175188201357889536)
- [HTTPS 配置指南](https://edgeone.ai/document/configuring-an-https-certificate)

## 许可证

MIT
