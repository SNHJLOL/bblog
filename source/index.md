---
title: index
date: 2026-02-25 17:54:32
layout: false
type: "index"
comments: false
---
<!-- 自定义主页核心内容 -->
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>你的博客名称 - 首页</title>
    <!-- 引入主题默认样式（保留全局风格） -->
    <link rel="stylesheet" href="/css/main.css">
    <!-- 自定义样式（可根据喜好修改） -->
    <style>
        /* 主页容器 */
        .home-container {
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 20px;
            text-align: center;
        }
        /* 头像样式 */
        .avatar {
            width: 150px;
            height: 150px;
            border-radius: 50%;
            border: 4px solid #eee;
            margin-bottom: 20px;
        }
        /* 昵称/标题 */
        .nickname {
            font-size: 28px;
            font-weight: bold;
            color: #333;
            margin-bottom: 10px;
        }
        /* 简介 */
        .intro {
            font-size: 16px;
            color: #666;
            line-height: 1.6;
            margin-bottom: 30px;
        }
        /* 导航按钮 */
        .nav-buttons {
            display: flex;
            justify-content: center;
            gap: 15px;
            flex-wrap: wrap;
            margin-bottom: 40px;
        }
        .nav-btn {
            padding: 10px 20px;
            background: #4285f4;
            color: white;
            border-radius: 8px;
            text-decoration: none;
            transition: background 0.3s;
        }
        .nav-btn:hover {
            background: #3367d6;
        }
        /* 统计模块 */
        .stats {
            display: flex;
            justify-content: center;
            gap: 30px;
            color: #888;
            font-size: 14px;
        }
    </style>
</head>
<body>
    <div class="home-container">
        <!-- 头像（替换为你的图片地址） -->
        <img src="/images/avatar.jpg" alt="头像" class="avatar">
        <!-- 昵称 -->
        <div class="nickname">喜</div>
        <!-- 个人简介 -->
        <div class="intro">
            欢迎来到我的个人博客！<br>
            分享编程、学习、生活的点滴，保持热爱，奔赴山海。
        </div>
        <!-- 导航按钮（替换为你的链接） -->
        <div class="nav-buttons">
            <a href="/" class="nav-btn">首页</a>
            <a href="/archives/" class="nav-btn">文章归档</a>
            <a href="/tags/" class="nav-btn">标签</a>
            <a href="/about/" class="nav-btn">关于我</a>
        </div>
        <!-- 简单统计（可选） -->
        <div class="stats">
            <div>文章数：XX</div>
            <div>建站时间：2026.02</div>
        </div>
    </div>
</body>
</html>