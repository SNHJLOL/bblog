# 用 AirScript 实现 WPS 在线文档自动化：定时抓取数据并自动填充

作为一名经常需要处理业务数据的职场人，每天手动从指定平台抓取数据、整理到 WPS 表格里的重复工作，不仅耗时还容易出错。最近我基于 WPS 在线文档的 AirScript 脚本能力，实现了一套自动化流程 —— 定时抓取指定日期范围的业务数据，自动提取关键指标并填充到表格指定单元格，彻底解放了重复劳动。今天就来分享这个实用的自动化脚本开发过程。

## 一、需求背景

日常工作中，每天都要从各大平台获取昨天的业绩，和昨日为止的当月业绩，填写到领导的在线表里

1. 

## 二、核心实现思路

1. 自动计算时间范围（当月 1 日至昨日）
2. 抓包并请求（或调用平台 API） 获取指定维度的订单数据
3. 根据响应回来的数据进行相应的处理
4. 填入相应单元格，并进行调试
5. 设置定时任务

### 1. 时间处理：自动计算日期范围

这里以某SAAS平台后台距离

首先要解决的是日期的自动计算，不需要手动修改日期参数：

```js
// 获取当前时间戳减去一天的毫秒数，得到昨日日期
const yesterday = new Date(Date.now() - 86400000);
// 提取年、月、日并补零（确保两位数格式）
const year = yesterday.getFullYear();
const month = String(yesterday.getMonth() + 1).padStart(2, '0');
const day = String(yesterday.getDate()).padStart(2, '0');
// 拼接成YYYY-MM-DD格式的昨日日期和当月1日
const pre = `${year}-${month}-${day}`;
const day1 = `${year}-${month}-01`;
```

这里需要注意`getMonth()`返回的是 0-11 的数字，所以要加 1；`padStart(2, '0')`确保月份和日期始终是两位数（比如 1 月转为 01）。

### 2. API 请求：构造请求参数并发起请求

AirScript 内置了`HTTP.fetch`方法，可以直接发起网络请求。需要重点处理：

- 请求头（headers）：完整复刻浏览器的请求头，确保接口鉴权通过
- 请求体：按接口要求构造参数，并用`encodeURIComponent`处理 JSON 字符串

```js
// 配置请求头（模拟浏览器请求）
const headers = {
  "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 ...",
  "Content-Type": "application/x-www-form-urlencoded;charset=UTF-8",
  "Cookie": "JSESSIONID=xxx; CURRENT_ROLERELATION_ID=xxx..." // 替换为实际有效Cookie
  // 其他必要请求头...
};

// 构造查询参数
const queryParam = {
  "dimensions":["billDate"],
  "pageSize":500,
  "pageNum":1,
  "filters":[
    {"fieldCode":"order_source","type":25,"values":["2"]}, // 指定渠道
    {"fieldCode":"bill_date","type":20,"values":[day1, pre]} // 时间范围
  ],
  "boolFormat":0,
  "dashboardCode":"xxx",
  "bizType":"M000000033"
};
// 构造符合接口要求的请求体
const requestBody = `menuId=xxx&query=${encodeURIComponent(JSON.stringify(queryParam))}`;

// 发起POST请求，设置10秒超时
const response = HTTP.fetch(postUrl, {
  method: "POST",
  headers: headers,
  body: requestBody,
  timeout: 10000,
  httpVersion: "1.1"
});
```

### 3. 数据解析与表格填充

接口返回数据后，需要提取关键信息并写入 WPS 表格：

```
// 解析响应数据
const responseText = response.text();
const responseData = JSON.parse(responseText);
const datas = responseData?.resultObject?.datas || [];
const sheet = Application.ActiveSheet;

// 无数据时给出提示
if (datas.length === 0) {
  sheet.Range("A1").Value2 = "未提取到datas数据";
  return;
}

// 提取第三列的字段名
const firstItem = datas[0];
let thirdColumnKey = "";
if (typeof firstItem === "object" && firstItem !== null) {
  const allKeys = Object.keys(firstItem);
  thirdColumnKey = allKeys.length >= 3 ? allKeys[2] : "";
}

// 提取最后一项的第三列数据，写入N8
let lastItemThirdValue = "";
const lastItem = datas[datas.length - 1];
if (thirdColumnKey && typeof lastItem === "object" && lastItem !== null) {
  lastItemThirdValue = lastItem[thirdColumnKey] || "无数据";
} else {
  lastItemThirdValue = "数据格式异常";
}
sheet.Range("N8").Value2 = lastItemThirdValue;

// 计算第三列数据总和，写入N11
let totalSum = 0;
datas.forEach(item => {
  if (thirdColumnKey && typeof item === "object" && item !== null) {
    const value = Number(item[thirdColumnKey]);
    if (!isNaN(value)) {
      totalSum += value;
    }
  }
});
sheet.Range("N11").Value2 = totalSum;

// 写入昨日星期数（getDay()返回0-6，0是周日，所以+1转为1-7）
sheet.Range("N23").Value2 = yesterday.getDay() + 1;
```

### 4. 异常处理

用`try-catch`捕获整个流程中的异常，确保脚本执行失败时能给出明确提示：

javascript



运行









```
try {
  // 核心业务逻辑...
} catch (error) {
  Application.ActiveSheet.Range("A1").Value2 = "执行失败：" + error.message;
  console.error("❌ 执行失败：", error.message);
}
```

## 三、定时任务配置

WPS 在线文档的 AirScript 支持设置定时任务，只需要：

1. 在脚本编辑器中完成代码编写并保存
2. 点击「定时执行」按钮，配置执行频率（比如每天早上 9 点）
3. 确认授权，脚本就会按设定时间自动运行

## 四、踩坑与优化

1. **Cookie 过期问题**：接口依赖的 Cookie 会定期失效，需要定期更新 headers 中的 Cookie 值，后续可以考虑对接登录接口自动获取 Cookie
2. **数据格式兼容**：增加了`typeof`和`isNaN`判断，避免因接口返回数据格式变化导致脚本崩溃
3. **超时设置**：设置 10 秒超时时间，避免因网络问题导致脚本长时间阻塞
4. **空值处理**：对无数据、字段不存在等情况都做了兜底，确保表格不会写入空值或错误值

## 五、总结

通过 AirScript，我把原本每天需要手动操作 10 分钟的工作，变成了全自动执行的流程，不仅节省了时间，还避免了手动操作的错误。其实 WPS AirScript 的能力远不止这些，它可以对接各种 API、操作表格 / 文档、甚至联动其他办公软件，只要结合实际工作场景，就能实现很多重复性工作的自动化。

对于职场人来说，不需要成为专业的程序员，只要掌握基础的脚本逻辑，就能用这些工具大幅提升工作效率。建议大家多尝试挖掘办公软件的自动化能力，把时间花在更有价值的事情上。

### 关键点回顾

1. AirScript 可以通过`HTTP.fetch`发起网络请求，结合`Application`对象操作 WPS 表格，实现数据抓取 + 填充的自动化；
2. 日期处理要注意月份偏移（`getMonth()+1`）和补零操作，确保符合接口的日期格式要求；
3. 数据解析时要做好空值、格式异常的兜底处理，定时任务配置可实现脚本自动执行。