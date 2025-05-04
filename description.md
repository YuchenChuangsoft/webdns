# 高级DNS解析器使用说明文档

## 概述

高级DNS解析器是一个基于HTML和JavaScript的前端工具，它允许用户通过指定一系列参数动态地将域名转换为IP地址，并根据提供的附加信息进行重定向。该工具支持从页面内置的数据源或外部JSON文件中获取域名与IP地址的映射关系，并支持HTTPS、自定义端口、子目录以及额外的GET请求参数。

## 文件结构

```
project/
├── index.html       # 主页面
└── dns.json         # 外部DNS数据文件（当 source=1 时使用）
```

## 参数说明

| 参数名     | 含义                         | 必需/可选 | 示例值                       |
|------------|------------------------------|-----------|-----------------------------|
| hostname   | 要解析的域名                 | 必需      | `www.abc.def`               |
| source     | 数据源（0=内置，1=外部json）  | 可选，默认0 | `0` 或 `1`                  |
| secure     | 是否使用 HTTPS               | 可选，默认false | `true`                   |
| port       | 自定义端口号                 | 可选，默认80（HTTP）或443（HTTPS） | `8080`                    |
| path       | 子路径                       | 可选，默认为空字符串 | `admin`                   |
| query      | 目标URL上的GET查询字符串     | 可选      | `id=123&name=test`          |

## 使用示例

### 示例 1: 基本跳转

访问以下URL将会解析`hostname`参数对应的IP地址，并进行重定向：

```
http://yourserver/path/to/index.html?hostname=www.abc.def
```

- **预期结果**：页面将显示“正在跳转到: http://10.103.1.2”，然后在1秒后重定向至 `http://10.103.1.2`。

### 示例 2: 使用HTTPS协议

```
http://yourserver/path/to/index.html?hostname=www.abc.def&secure=true
```

- **预期结果**：页面将显示“正在跳转到: https://10.103.1.2”，然后在1秒后重定向至 `https://10.103.1.2`。

### 示例 3: 自定义端口号

```
http://yourserver/path/to/index.html?hostname=www.abc.def&port=8080
```

- **预期结果**：页面将显示“正在跳转到: http://10.103.1.2:8080”，然后在1秒后重定向至 `http://10.103.1.2:8080`。

### 示例 4: 包含所有参数

```
http://yourserver/path/to/index.html?hostname=www.abc.def&source=1&secure=true&port=8443&path=admin&query=id=123&name=test
```

- **预期结果**：页面将显示“正在跳转到: https://10.103.1.2:8443/admin?id=123&name=test”，然后在1秒后重定向至 `https://10.103.1.2:8443/admin?id=123&name=test`。

## 注意事项

1. **安全性**：直接通过JavaScript重定向用户可能会带来安全风险（如开放重定向漏洞），请确保采取适当的防护措施。
2. **默认值**：
   - 如果未提供`source`参数，默认值为0，即使用内置数据。
   - 如果未提供`secure`参数，默认值为false，即使用HTTP。
   - 如果未提供`port`参数，默认值为80（对于HTTP）或443（对于HTTPS）。
   - 如果未提供`path`参数，默认值为空字符串，表示根路径。
   - 如果未提供`query`参数，默认值为空字符串，表示没有额外的GET参数。
3. **错误处理**：如果无法找到与`hostname`匹配的IP地址，或者遇到网络问题，页面会显示相应的错误消息。

## 如何运行

1. 将`index.html`和`dns.json`放在同一目录下。
2. 在本地启动一个Web服务器（例如使用Python的`http.server`模块）：
   ```bash
   python -m http.server 8000
   ```
3. 在浏览器中访问类似以下的URL：
   ```
   http://localhost:8000/index.html?hostname=www.abc.def
   ```

