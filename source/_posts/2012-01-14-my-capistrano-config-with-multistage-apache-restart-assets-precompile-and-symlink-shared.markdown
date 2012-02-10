---
layout: post
title: "我的 capistrano 完整設定檔說明"
date: 2012-01-14 16:09
comments: true
categories: [rubygems, capistrano, deploy] 
---

capistrano 是一套強大的佈署工具, 今天提供小弟的設定檔內容給大家參考

<!-- more -->

rvm 和 bundler

```ruby config/deploy.rb
$:.unshift(File.expand_path('./lib', ENV['rvm_path'])) # Add RVM's lib directory to the load path.
require "rvm/capistrano" # Load RVM's capistrano plugin.
require 'bundler/capistrano'
```

capistrano color, 讓佈署過程中的訊息內容上色  
Gemfile 必須先加上 gem 'capistrano_colors'

```ruby config/deploy.rb
begin
  require 'capistrano_colors'
rescue LoadError
  puts "`gem install capistrano_colors` to get output more userfriendly."
end
```

cron 設定, 透過 whenever gem 在 deploy 時, 更新 cron  
Gemfile 要有 gem 'whenever'

```ruby config/deploy.rb
require "whenever/capistrano"
```

multistages 設定, 讓 server 端的設定和佈署工作各自獨立管理, 同時資源佈署多個 server

```ruby config/deploy.rb
require 'capistrano/ext/multistage'
set :stages,        %w(staging production)
set :default_stage, "staging"
```

基本設定, 包含 git

```ruby config/deploy.rb
set :application, "foo"
set :repository,  "git@github.com:marsz/foo.git"

set :scm, :git

set :use_sudo, false
```

設定 deploy:restart, capistrano 預設是空的, 所以重起 rack 的部份一定要自己寫, 以下範例以 <a href="http://www.modrails.com/documentation/Users%20guide%20Apache.html#_redeploying_restarting_the_ruby_on_rails_application" target="_blank">passenger + apache</a> 的重啟方式做為參考

```ruby config/deploy.rb
# namespace :deploy 內
namespace :deploy do
  # .....
  task :restart, :roles => :app, :except => { :no_release => true } do
    run "touch #{current_path}/tmp/restart.txt"
  end
  # .....
end
```

設定 symlink_shared, 有一些沒進 version control 的 config 檔案, 要在佈署時, link 過去, 例如 config/database.yml, 以下僅供參考, 實際 link 內容須自己寫

```ruby config/deploy.rb
# namespace :deploy 內
namespace :deploy
  # ....
  task :symlink_shared, :roles => [:app] do
    config_files = [:database, :redis]
    symlink_hash = {}
    config_files.each do |fname|
      from = "#{shared_path}/config/#{fname}.yml"
      to = "#{release_path}/config/#{fname}.yml"
      run "ln -s #{from} #{to}"
    end
  end
  # ....
end

# 把 symlink_shared 內容掛進去 deploy 中
before "bundle:install", "deploy:symlink_shared"
```

tail log, 要線上 debug 時可用

```ruby config/deploy.rb
task :tail_log, :roles => :app do
  run "tail -f -n 100 #{shared_path}/log/#{rails_env}.log"
end

# 執行 cap production tail_log 即可
```

使用了 multistage 後, 各個 server 的設定檔必須放在 config/deploy/ 下, 檔名對應 stage 名稱  
例如 config/deploy/production.rb
以下為 stage 設定檔內容

預設 rails env

```ruby config/deploy/production.rb
set :rails_env, "production"
```

github 分支

```ruby config/deploy/production.rb
set :branch, 'master'
```

ssh 登入相關, 建議多利用 <a href="http://www.eng.cam.ac.uk/help/jpmg/ssh/authorized_keys_howto.html" target="_blank"> SSH 免密碼登入 </a>

```ruby config/deploy/production.rb
set :user, 'marsz'
set :domain, 'xxx.com'
server "#{domain}", :web, :app, :db, :primary => true
```

佈署的 dir path

```ruby config/deploy/production.rb
set :deploy_to, "/path/to/app"
```

佈署指令

```
cap production setup # 第一次佈署環境建立
cap production deploy # 進行佈署
cap production deploy:migrations # 進行佈署並且跑 migration
```

若想要在 cap deploy 時也執行 rake assets:precompile  
可以參考 <a href="http://rubyist.marsz.tw/blog/2011-12-25/assets-precompile-in-capistrano-deploy/" target="_blank">這篇</a>
