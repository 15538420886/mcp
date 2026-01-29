# EdgeOne Pages MCP

An MCP service for deploying HTML content, folders, or full-stack projects to EdgeOne Pages and obtaining publicly accessible URLs.

<a href="https://glama.ai/mcp/servers/@TencentEdgeOne/edgeone-pages-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@TencentEdgeOne/edgeone-pages-mcp/badge" alt="EdgeOne Pages MCP server" />
</a>

## Demo

### Deploy HTML

![](https://cdnstatic.tencentcs.com/edgeone/pages/assets/U_GpJ-1746519327306.gif)

### Deploy Folder

![](https://cdnstatic.tencentcs.com/edgeone/pages/assets/kR_Kk-1746519251292.gif)

## Requirements

- Node.js 18 or higher

## Deployment on Tencent Cloud

This MCP service supports multiple deployment options. Choose the one that best fits your needs.

### Option 1: Deploy Self-Hosted Version to EdgeOne Pages (Recommended)

This is the simplest and fastest deployment method, suitable for scenarios requiring HTTP access.

#### Step 1: One-Click Deploy to EdgeOne Pages

1. Visit the [self-hosted repository](https://github.com/TencentEdgeOne/self-hosted-pages-mcp)
2. Click the "One-Click Deploy" button to deploy the service to EdgeOne Pages
3. Complete the deployment configuration as prompted

#### Step 2: Configure KV Storage

After deployment, configure KV storage for storing website files:

1. Go to EdgeOne Pages console
2. Find KV Storage configuration
3. Follow the [KV Storage Setup Guide](https://pages.edgeone.ai/document/kv-storage)

#### Step 3: Bind Custom Domain (Optional)

1. Add a custom domain in project settings
2. Configure DNS CNAME record
3. Wait for domain verification
4. Refer to [Custom Domain Binding Guide](https://pages.edgeone.ai/document/custom-domain)

#### Step 4: Configure MCP Client

After deployment, add to your MCP configuration file:

```json
{
  "mcpServers": {
    "edgeone-pages": {
      "url": "https://your-custom-domain.com/mcp-server"
    }
  }
}
```

**Advantages:**
- ✅ Zero server maintenance cost
- ✅ Auto-scaling and high availability
- ✅ Global edge acceleration
- ✅ Custom domain and HTTPS support

### Option 2: Deploy on Tencent Cloud CVM

Suitable for scenarios requiring full server control or stdio transport.

#### Step 1: Prepare CVM Instance

1. Log in to [Tencent Cloud Console](https://console.cloud.tencent.com/)
2. Create or select a CVM instance
3. Recommended configuration:
   - **OS**: Ubuntu 20.04 LTS or CentOS 7+
   - **CPU**: 2 cores or more
   - **Memory**: 4GB or more
   - **Network**: Configure public IP or elastic IP

#### Step 2: Install Node.js Environment

**Ubuntu/Debian:**
```bash
# Update system packages
sudo apt update

# Install Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify installation
node --version
npm --version
```

**CentOS/RHEL:**
```bash
# Install Node.js 18.x
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs

# Verify installation
node --version
npm --version
```

#### Step 3: Deploy MCP Service

```bash
# Clone project (or upload project files)
git clone https://github.com/TencentEdgeOne/edgeone-pages-mcp.git
cd edgeone-pages-mcp

# Install dependencies
npm install

# Build project
npm run build

# Configure environment variables
cat > .env << EOF
EDGEONE_PAGES_API_TOKEN=your-api-token-here
EDGEONE_PAGES_PROJECT_NAME=your-project-name
EOF
```

#### Step 4: Configure Process Management (Using PM2)

```bash
# Install PM2
sudo npm install -g pm2

# Start service
pm2 start dist/index.js --name edgeone-pages-mcp

# Set auto-start on boot
pm2 startup
pm2 save

# Check service status
pm2 status
pm2 logs edgeone-pages-mcp
```

#### Step 5: Configure Firewall

```bash
# Ubuntu/Debian (UFW)
sudo ufw allow 22/tcp    # SSH
# If using HTTP transport, open corresponding port
# sudo ufw allow 8080/tcp

# CentOS (firewalld)
sudo firewall-cmd --permanent --add-service=ssh
# sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

#### Step 6: Configure MCP Client

If using stdio transport (local or SSH):

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

**Advantages:**
- ✅ Full control over server environment
- ✅ Support for stdio transport
- ✅ Suitable for enterprise intranet environments

### Option 3: Deploy Using Tencent CloudBase

Suitable for scenarios requiring quick deployment with Tencent Cloud managed services.

#### Step 1: Create CloudBase Environment

1. Visit [Tencent CloudBase Console](https://console.cloud.tencent.com/tcb)
2. Create a new environment or select an existing one
3. Choose Node.js 18+ runtime

#### Step 2: Deploy Code

1. Select "Cloud Functions" in CloudBase console
2. Create a new function or upload code
3. Configure function entry as `index.js`
4. Set environment variables:
   - `EDGEONE_PAGES_API_TOKEN`
   - `EDGEONE_PAGES_PROJECT_NAME`

#### Step 3: Configure HTTP Trigger

1. Add HTTP trigger in function configuration
2. Configure access path and authentication
3. Get trigger URL

#### Step 4: Configure MCP Client

```json
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "url": "https://your-function-url.tencentcloudapi.com"
    }
  }
}
```

**Advantages:**
- ✅ No server management required
- ✅ Auto-scaling
- ✅ Integrated with Tencent Cloud services

### Security Recommendations

Regardless of deployment method, it's recommended to:

1. **Protect API Token**:
   - Use environment variables for sensitive information
   - Don't commit `.env` files to repository
   - Rotate API tokens regularly

2. **Network Security**:
   - Use HTTPS for service access
   - Configure firewall rules to limit access sources
   - Use VPN or intranet access (if applicable)

3. **Monitoring and Logging**:
   - Configure log collection and monitoring
   - Set up alert rules
   - Regularly check service status

### Troubleshooting

**Issue: Service won't start**
- Check if Node.js version meets requirements (18+)
- Verify dependencies are correctly installed: `npm install`
- Check logs: `pm2 logs` or check cloud function logs

**Issue: Invalid API Token**
- Verify token is correctly configured
- Check if token has expired
- Refer to [API Token Documentation](https://edgeone.ai/document/177158578324279296)

**Issue: Deployment failed**
- Check network connection
- Verify API token permissions
- Check detailed error logs

## MCP Configuration

### stdio MCP Server

Full-featured MCP service that supports the `deploy_folder` tool for deploying full-stack projects.

```jsonc
// Tencent Cloud International (Default)
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "timeout": 600,
      "command": "npx",
      "args": ["edgeone-pages-mcp-fullstack@latest"]
    }
  }
}

// Tencent Cloud China
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "timeout": 600,
      "command": "npx",
      "args": ["edgeone-pages-mcp-fullstack@latest", "--region", "china"]
    }
  }
}
```

The following MCP Server will be deprecated soon:

Supports both `deploy_html` and `deploy_folder_or_zip` tools.

```jsonc
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "command": "npx",
      "args": ["edgeone-pages-mcp@latest"],
      "env": {
        // Optional. 
        // If you need to deploy folders or zip files to 
        // EdgeOne Pages projects, provide your EdgeOne Pages API token.
        // How to obtain your API token: 
        // https://edgeone.ai/document/177158578324279296
        "EDGEONE_PAGES_API_TOKEN": "",
        // Optional. Leave empty to create a new EdgeOne Pages project.
        // Provide a project name to update an existing project.
        "EDGEONE_PAGES_PROJECT_NAME": ""
      }
    }
  }
}
```

### Streaming HTTP MCP Server

For MCP clients that support HTTP streaming, only supports the `deploy_html` tool.

```json
{
  "mcpServers": {
    "edgeone-pages-mcp-server": {
      "url": "https://mcp-on-edge.edgeone.site/mcp-server"
    }
  }
}
```

## Tool Details

### deploy_html Tool

#### Architecture Design

![EdgeOne Pages MCP Architecture](./assets/architecture.svg)

The architecture diagram shows the complete workflow of the `deploy_html` tool:

1. Large Language Model generates HTML content
2. Content is sent to the EdgeOne Pages MCP Server
3. MCP Server deploys the content to EdgeOne Pages Edge Functions
4. Content is stored in EdgeOne KV Store for fast edge access
5. MCP Server returns a publicly accessible URL
6. Users can access the deployed content via browser with fast edge delivery

#### Implementation Details

This tool integrates with EdgeOne Pages Functions to deploy static HTML content:

1. **EdgeOne Pages Functions** - A serverless computing platform that supports executing JavaScript/TypeScript code at the edge

2. **Core Implementation Features**:

   - Uses EdgeOne Pages KV storage to save and serve HTML content
   - Automatically generates publicly accessible URLs for each deployment
   - Provides comprehensive API error handling and feedback

3. **How It Works**:
   - MCP server receives HTML content through the `deploy_html` tool
   - Connects to EdgeOne Pages API to obtain the base URL
   - Deploys HTML content using the EdgeOne Pages KV API
   - Returns an immediately accessible public URL

For more information, refer to the [EdgeOne Pages Functions documentation](https://edgeone.ai/document/162227908259442688) and [EdgeOne Pages KV Storage Guide](https://edgeone.ai/document/162227803822321664).

The source code is open source and can be self-deployed with custom domain binding: https://github.com/TencentEdgeOne/self-hosted-pages-mcp

### deploy_folder Tool

This tool supports deploying complete projects to EdgeOne Pages:

- Supports full deployment of static website projects
- Supports deployment of full-stack applications
- Option to update existing projects or create new ones

### Custom Domain Configuration

EdgeOne Pages MCP supports custom domain deployment. After configuring a custom domain in the EdgeOne Pages console, the deployment will automatically use your custom domain instead of the default temporary URL.

#### Benefits of Custom Domain

- **Brand Display**: Use your own brand domain name to enhance professional image
- **SEO Optimization**: Beneficial for search engine indexing and ranking
- **Better Control**: Full control of DNS records
- **Flexibility**: Configure multiple custom domains for different markets or purposes

#### How to Configure Custom Domain

**Step 1: Add Custom Domain in Console**

1. After deploying your project, open the [EdgeOne Pages Console](https://console.tencentcloud.com/edgeone/pages)
2. Navigate to your project settings page
3. Find the "Domain Management" module
4. Click "Add Custom Domain" button
5. Input your root domain (e.g., `example.com`) or subdomain (e.g., `www.example.com`)

**Step 2: Configure DNS CNAME Record**

1. The console will display a CNAME record value (e.g., `a4285573.xxx.example.com.dns.edgeone.site.`)
2. Go to your domain registrar's DNS management page
3. Add a CNAME record with:
   - **Type**: CNAME
   - **Name**: Your subdomain (e.g., `www`) or `@` for root domain
   - **Value**: The CNAME value provided by EdgeOne Pages
   - **TTL**: 600 seconds (recommended for faster propagation)
4. Save the DNS record

**Step 3: Verify Domain**

1. Return to the EdgeOne Pages console
2. Click "Verify" button or wait for automatic DNS detection
3. DNS records typically take effect within minutes, up to 48 hours maximum
4. Once verified, the domain status will change to "Pass"

**Step 4: Configure HTTPS (Recommended)**

1. After domain verification, go to "HTTPS Configuration" section
2. Apply for a free SSL certificate or use a managed SSL certificate
3. The certificate will be automatically configured for your custom domain

**Step 5: Deploy with Custom Domain**

After completing the above steps, when you deploy using `deploy_folder_or_zip`:

- If your project has a custom domain configured and verified (Status: "Pass"), the deployment will automatically use your custom domain
- The returned URL will be `https://your-custom-domain.com` instead of the temporary URL
- No additional configuration is needed in the MCP tool

#### Important Notes

- **ICP Filing Requirement**: If your project's acceleration region is "Chinese Mainland Availability Zone" or "Global Availability Zone (including Chinese Mainland)", you must complete ICP filing with the Ministry of Industry and Information Technology before adding custom domains
- **Domain Status**: Only domains with status "Pass" will be automatically used in deployments
- **Multiple Domains**: If multiple custom domains are configured, the first domain with "Pass" status will be used
- **Console URL**: After deployment, you can access the project console URL provided in the deployment result to manage domains

#### Example Deployment Result with Custom Domain

```json
{
  "type": "custom",
  "url": "https://www.example.com",
  "projectId": "your-project-id",
  "projectName": "your-project-name",
  "consoleUrl": "https://console.tencentcloud.com/edgeone/pages/project/your-project-id/index"
}
```

For more information, refer to:
- [Custom Domain Documentation](https://edgeone.ai/document/175201436224495616)
- [DNS CNAME Configuration Guide](https://edgeone.ai/document/175188201357889536)
- [HTTPS Configuration Guide](https://edgeone.ai/document/configuring-an-https-certificate)

## License

MIT
