# Chonic

## shell

```shell
bundle exec jekyll s
```

## environment

- 001 Ruby::RubyInstaller: [ruby](https://github.com/oneclick/rubyinstaller2/releases/download/RubyInstaller-3.4.5-1/rubyinstaller-devkit-3.4.5-1-x64.exe) 
- 002 配置镜像: `bundle config mirror.https://rubygems.org https://gems.ruby-china.com`
- 003 安装依赖: `bundle install` （记得删除旧版本gemfile.lock）
- 004 启动: `bundle exec jekyll s`

## 项目结构

```yaml
D:.
├─assets # 图床
│  ├─2025-05
│  ├─2025-06
│  ├─2025-07
│  ├─2025-08
│  ├─books
│  ├─css
│  ├─img
│  ├─lib
│  └─mp4
├─_data # 数据
├─_drafts # 草稿
├─_oposts # 直接
│  ├─Direct # 直接uri
│  └─local 
├─_posts # 一般
│  ├─local 
│  └─OUT
│      ├─2025 # 日记
│      ├─complex # 情节
│      ├─log # 存档
│      ├─travel # 旅游
│      └─tutorial # 教程
├─_site # 静态文件
└─_tabs # 侧栏
```