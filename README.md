# 招投标大数据服务

[该MCP服务提供全面的招投标信息查询功能，包括中标统计、招标统计、采购统计、招投标信息查询、拟建项目查询等，帮助用户进行招投标分析和市场机会发现。](https://www.handaas.com/)


## 主要功能

- 🔍 企业关键词模糊搜索
- 🏆 企业中标统计分析
- 📋 企业招标统计分析
- 💼 企业采购统计分析
- 📄 招投标信息查询
- 🏗️ 拟建项目查询
- 🔎 招投标信息搜索

## 环境要求

- Python 3.10+
- 依赖包：python-dotenv, requests, mcp

## 本地快速启动

### 1. 克隆项目
```bash
git clone https://github.com/handaas/bidding-mcp-server
cd bidding-mcp-server
```

### 2. 创建虚拟环境&安装依赖

```bash
python -m venv mcp_env && source mcp_env/bin/activate
pip install -r requirements.txt
```

### 3. 环境配置

复制环境变量模板并配置：

```bash
cp .env.example .env
```

编辑 `.env` 文件，配置以下环境变量：

```env
INTEGRATOR_ID=your_integrator_id
SECRET_ID=your_secret_id
SECRET_KEY=your_secret_key
```

### 4. streamable-http启动服务

```bash
python server/mcp_server.py streamable-http
```

服务将在 `http://localhost:8000` 启动。

#### 支持启动方式 stdio 或 sse 或 streamable-http

### 5. Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "handaas-mcp-server": {
      "type": "streamableHttp",
      "url": "http://127.0.0.1:8000/mcp"
    }
  }
}
```

## STDIO版安装部署

### 设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "bidding-mcp-server": {
      "command": "uv",
      "args": ["run", "mcp", "run", "{workdir}/server/mcp_server.py"],
      "env": {
        "PATH": "{workdir}/mcp_env/bin:$PATH",
        "PYTHONPATH": "{workdir}/mcp_env",
        "INTEGRATOR_ID": "your_integrator_id",
        "SECRET_ID": "your_secret_id",
        "SECRET_KEY": "your_secret_key"
      }
    }
  }
}
```

## 使用官方Remote服务

### 1. 直接设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "bidding-mcp-server":{
      "type": "streamableHttp",
      "url": "https://mcp.handaas.com/bidding/bidding_bigdata?token={token}"  
      }
  }
}
```

### 注意：integrator_id、secret_id、secret_key及token需要登录 https://www.handaas.com/ 进行注册开通平台获取


## 可用工具

### 1. bid_bigdata_fuzzy_search
**功能**: 企业关键词模糊查询

根据提供的企业名称、人名、品牌、产品、岗位等关键词模糊查询相关企业列表。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 查询各类信息包含匹配关键词的企业
- `pageIndex` (可选): 分页开始位置
- `pageSize` (可选): 分页结束位置 - 一页最多获取50条数据

**返回值**:
- `total`: 总数
- 其他企业相关信息

### 2. bid_bigdata_bid_win_stats
**功能**: 企业中标统计分析

根据企业名称、统一社会信用代码等获取企业标讯信息中中标信息统计项，包括标的分布、金额分布、区域分布及中标趋势等。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型 - name：企业名称，nameId：企业id，regNumber：注册号，socialCreditCode：统一社会信用代码

**返回值**:
- `winbidAmountStatList`: 中标金额分布
  - `range`: 金额范围
  - `percent`: 比例
  - `count`: 数量
- `winbidAreaStat`: 区域分布
  - `area`: 区域
  - `count`: 数量
- `winbidStatList`: 中标标的分布
  - `subjectMatter`: 标的物
  - `count`: 数量
  - `percent`: 比例
- `winbidTrend`: 中标趋势
  - `year`: 年份
  - `count`: 数量

### 3. bid_bigdata_bidding_info
**功能**: 企业招投标信息查询

根据输入的企业标识符，查询并返回该企业参与的招投标信息，包括招投标公告类型、项目地区、公告详情及与之相关的企业信息。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id
- `pageSize` (可选): 分页大小 - 一页最多获取50条数据
- `keywordType` (可选): 主体类型 - name：企业名称，nameId：企业id，regNumber：注册号，socialCreditCode：统一社会信用代码
- `pageIndex` (可选): 页码 - 从1开始

**返回值**:
- `total`: 总数
- `biddingId`: 招投标Id
- `infoType`: 招投标公告类型
- `projectRegion`: 项目地区
- `publishDate`: 公告发布时间
- `subjectMatterList`: 标的物
- `title`: 公告标题
- `role`: 招投标角色 - 招标，中标
- `projectAmount`: 项目金额
- `winningBidderList`: 中标企业
- `purchasingBidderList`: 招标企业

### 4. bid_bigdata_tender_stats
**功能**: 企业招标统计分析

根据企业名称、统一社会信用代码等获取企业标讯信息中招标信息统计项，包括标的分布、金额分布、区域分布及招标趋势等。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型 - name：企业名称，nameId：企业id，regNumber：注册号，socialCreditCode：统一社会信用代码

**返回值**:
- `tenderAmountStatList`: 招标金额分布
  - `range`: 金额范围
  - `percent`: 比例
  - `count`: 数量
- `tenderAreaStat`: 区域分布
  - `area`: 区域
  - `count`: 数量
- `tenderStatList`: 招标标的分布
  - `subjectMatter`: 标的物
  - `count`: 数量
  - `percent`: 比例
- `tenderTrend`: 招标趋势
  - `year`: 年份
  - `count`: 数量

### 5. bid_bigdata_procurement_stats
**功能**: 企业采购统计分析

根据企业名称或ID，获取企业采购统计信息，包括采购产品分布、采购区域分布等。

**参数**:
- `keywordType` (可选): 主体类型 - name：企业名称，nameId：企业id，regNumber：注册号，socialCreditCode：统一社会信用代码
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id

**返回值**:
- `purchasingProductStatList`: 采购产品分布
  - `count`: 采购数量
  - `percent`: 占比
  - `product`: 产品名称
- `purchasingAreaStatList`: 采购区域分布
  - `times`: 客户数
  - `area`: 地区

### 6. bid_bigdata_bid_search
**功能**: 招投标信息搜索

查询和筛选招投标信息，通过提供多种过滤条件返回符合条件的招投标公告详细信息。

**参数**:
- `matchKeyword` (可选): 搜索关键词 - 默认按最新发布时间返回全部
- `biddingType` (可选): 信息类型 - 招标类型枚举
- `biddingRegion` (可选): 项目地区 - 多选，支持省份，城市
- `biddingAnncPubStartTime` (可选): 发布开始日期 - 格式："2024-08-01"
- `biddingAnncPubEndTime` (可选): 发布结束日期 - 格式："2024-11-01"
- `searchMode` (可选): 搜索模式 - 标题匹配，标的物匹配，全文匹配
- `biddingProjectMaxAmount` (可选): 项目金额最大值 - 单位：万
- `biddingPurchasingType` (可选): 招标单位类型 - 政府，学校，医院等
- `biddingProjectMinAmount` (可选): 项目金额最小值 - 单位：万
- `pageIndex` (可选): 分页索引
- `pageSize` (可选): 分页大小 - 一页最多获取50条

**返回值**:
- `total`: 总数
- `biddingAnncTitle`: 公告标题
- `biddingContent`: 正文
- `biddingId`: 公告id
- `biddingInfoType`: 公告类型
- `biddingProjectType`: 项目类型
- `biddingPublishTime`: 公告时间
- `biddingEndTime`: 招标截止时间
- `biddingProjectID`: 项目编号
- `biddingAgentInfoList`: 招标代理机构信息列表
- `biddingPurchasingInfoList`: 招标单位相关信息列表
- `biddingWinningInfoList`: 中标单位相关信息列表
- `biddingRegion`: 招投标所属地区
- `hasFile`: 有无附件

### 7. bid_bigdata_planned_projects
**功能**: 拟建项目查询

查询企业拟建公告的信息，提供通过企业名称、注册号、社会信用代码或企业ID等多种方式检索拟建项目的相关详情。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id
- `pageIndex` (可选): 页码 - 从1开始
- `pageSize` (可选): 分页大小 - 一页最多获取50条数据
- `keywordType` (可选): 主体类型 - name：企业名称，nameId：企业id，regNumber：注册号，socialCreditCode：统一社会信用代码

**返回值**:
- `total`: 总数
- `ppId`: 项目id
- `deviceList`: 待采设备
- `ppRegion`: 建设地点
- `ppTitle`: 项目名称
- `ppContent`: 项目内容
- `ppPublishTime`: 发布时间
- `ppApprovalTime`: 评审时间

## 使用场景

1. **政府采购**: 查询合适的招标项目，了解招投标动态
2. **企业投标**: 查找投标机会，分析竞争对手情况
3. **市场分析**: 分析招投标市场趋势，制定商业策略
4. **风险评估**: 评估企业招投标能力和履约能力
5. **商业拓展**: 发现潜在商业机会和合作伙伴
6. **行业研究**: 了解特定行业的招投标活跃度

## 使用注意事项

1. **企业全称要求**: 在调用需要企业全称的接口时，如果没有企业全称则先调取bid_bigdata_fuzzy_search接口获取企业全称
2. **分页限制**: 一页最多获取50条数据
3. **时间格式**: 日期参数格式为"YYYY-MM-DD"
4. **金额单位**: 项目金额单位为万元
5. **地区筛选**: 支持按省份、城市进行多选筛选

## 使用提问示例

### bid_bigdata_fuzzy_search (企业关键词模糊搜索)
1. 帮我查找包含"中建"关键词的企业信息
2. 搜索与"中铁"相关的企业列表
3. 查询名称中包含"华为"的公司

### bid_bigdata_bid_win_stats (企业中标统计分析)
1. 分析中国建筑股份有限公司的中标统计情况
2. 查询华为技术有限公司的中标金额分布
3. 中铁建设集团的中标趋势分析

### bid_bigdata_bidding_info (企业招投标信息查询)
1. 查询中国建筑参与的招投标项目信息
2. 华为最近的招投标记录有哪些？
3. 中铁建设的招投标公告详情

### bid_bigdata_tender_stats (企业招标统计分析)
1. 分析国家电网的招标统计情况
2. 查询中石油的招标金额分布
3. 中国移动的招标趋势分析

### bid_bigdata_procurement_stats (企业采购统计分析)
1. 分析华为的采购产品分布情况
2. 查询阿里巴巴的采购区域分布
3. 腾讯的采购统计数据

### bid_bigdata_bid_search (招投标信息搜索)
1. 搜索最近的5G基站建设招标项目
2. 查找广东省的政府采购招标信息
3. 搜索金额在1000万以上的工程招标项目

### bid_bigdata_planned_projects (拟建项目查询)
1. 查询中国建筑的拟建项目信息
2. 华为有哪些拟建工程项目？
3. 中铁建设的拟建项目详情 