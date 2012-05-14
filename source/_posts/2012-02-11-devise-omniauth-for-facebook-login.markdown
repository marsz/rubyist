---
layout: post
title: "Devise + Omniauth 的 Facebook 登入範例"
date: 2012-02-11 01:20
comments: true
author: MarsZ
categories: ["Rubygems", "Devise", "Omniauth", "Facebook"]
---
以下範例應用於 rails 3.1.3 + devise 1.4.9 + oa-oauth 0.3.2  
直接使用 devise 中的 omniauthable 有許多彈性不足的問題  
因此小弟參考了網路上的作法, 加上因應某些需求的修改...

<!-- more -->

#### 參考

<a href="http://blog.railsrumble.com/blog/2010/10/08/intridea-omniauth" target="_blank">http://blog.railsrumble.com/blog/2010/10/08/intridea-omniauth</a>  

#### 步驟

(以下範例以 user 做為 model 名稱)  

安裝 gem  

```ruby
gem "devise", "1.4.9"
gem "oa-oauth", :require => "omniauth/oauth"
gem "oa-openid", :require => "omniauth/openid" # for google 或其他 openid auth
```

devise initial setup

```
rails g devise:install
rails g devise user
```

在 users 的 migration 中, 依照需求可自行選擇加入

```ruby
create_table(:users) do |t|
  t.string :name # 儲存使用者名稱
  t.confirmable # email 認證, 若未來有提供註冊流程產生新使用者的可能性, 就加吧
  ...
end

change_column :users,:email, :string, :null => true # 若登入 open id 的 provider 不提供 email, 則必須做此設定, 讓 email 可以為 null
```

建立對應 opend id 和 user 的 model - authorization  

```
rails g model authorization provider:string uid:string user_id:integer
```

user model 內容  
1. devise 的 module 比預設的多了 confirmable, 主要為了提供未來也能使用站內註冊流程的可能  
2. create_from_auth 處理 omniauth 丟過來的 auth_hash 並且以 email 為 unique key 新增 user

```ruby app/models/user.rb
class User < ActiveRecord::Base
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable,
         :confirmable # 加上 confirmable

  has_many :authorizations

  def self.create_from_auth!(hash)
    created_hash = {:email => hash[:user_info][:email], :name => hash[:user_info][:name] }
    user = (created_hash[:email].nil? ? nil : User.find_by_email(created_hash[:email])) || User.new(created_hash)
    if user.email
      user.confirm!
    elsif user
      user.save!
    end
    user
  end
end
```

authorization model 內容  
1. 對應 user 和 open id, 使其不重複  
2. #create_by_omniauth 處理從 omniauth 登入後的資料, 可以將 provider 的資料整合進指定的user(例如目前登入者) 或相同 email 的 user  
3. constant PROVIDERS 用以提供未來擴充 provider 的彈性  

```ruby
class Authorization < ActiveRecord::Base
  PROVIDERS = [:facebook]
  
  belongs_to :user
  validates_presence_of :user_id, :uid, :provider
  validates_uniqueness_of :uid, :scope => :provider
  
  def self.create_by_omniauth(hash, current_user = nil)
    hash = ActiveSupport::HashWithIndifferentAccess.new hash
    auth = find_from_hash(hash)
    unless auth
      current_user ||= User.create_from_auth!(hash)
      auth = Authorization.new(:user=>current_user,:uid=>hash[:uid],:provider=>hash[:provider])
      auth.save
    end
    auth
  end
  
  private
  
  def self.find_from_hash(hash)
    if hash[:provider] && hash[:uid]
      find_by_provider_and_uid(hash[:provider],hash[:uid])
    end
  end
end
```

設定 omniauth (facebook 登入的 app data), 同時讓 /auth/facebook 的 route 可以連接 fb 的 oauth, 相關可參考 <a href="https://github.com/intridea/omniauth" target="_blank">omniauth 的說明</a>  

```ruby config/initializers/omniauth.rb
Rails.application.config.middleware.use OmniAuth::Builder do
  config = YAML.load(File.open("#{Rails.root}/config/omniauth.yml"))[Rails.env]
  provider :facebook, config[:facebook][:api_id], config[:facebook][:api_secret]
end
```

```yml config/omniauth.yml
development:
  :facebook:
    :domain: "foo.com"
    :api_id: "12341234"
    :api_secret: "barbarbarbar"
test:
  :facebook:
    :domain: "foo.com"
    :api_id: "12341234"
    :api_secret: "barbarbarbar"
production:
  :facebook:
    :domain: "foo.com"
    :api_id: "12341234"
    :api_secret: "barbarbarbar"
```

如同 omniauth 的做法, 把接 facebook (或其他 provider) 的 route 和 controller 寫好  

```ruby 
rails g controller sessions
```

有可能登入 facebook 會 fail, 所以也要把 fail 的部份寫好

```ruby app/controllers/sessions_controller.rb
class SessionsController < ApplicationController
  def create
    @auth = Authorization.create_by_omniauth(auth_hash, current_user)
    if @auth.user
       sign_in_and_redirect :user, @auth.user
    else
      raise do
        logger.info "auth_hash: #{auth_hash.inspect}"
        logger.info "@auth: #{@auth.errors.inspect}"
        redirect_to new_user_session_path
      end
    end  
  end

  def failure
    flash[:error] = params[:message]
    redirect_to new_user_session_path
  end
  
  private 

  def auth_hash
    request.env['omniauth.auth']
  end
end
```

設定 route, 包含 omniauth 和 devise  

```ruby config/routes.rb
  match '/auth/:provider/callback', :to => 'sessions#create'
  match '/auth/failure' => 'sessions#failure'
  devise_for :users do
    get '/users/sign_in', :to => 'devise/sessions#new', :as => :new_user_session
    get '/users/sign_out' => 'devise/sessions#destroy', :as => :user_sign_out
  end
```

view 裡面的 Facebook 登入按鈕 (html)

```html
<a href="/auth/facebook">Facebook 登入</a>
```

若希望 Facebook 登入後可以導回登入前的頁面, 則可以做以下設定  
主要參考 <a href="https://github.com/plataformatec/devise/wiki/How-To:-Redirect-to-a-specific-page-on-successful-sign-in" target="_blank">Devise Wiki</a>  

```ruby app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  private
  
  def after_sign_in_path_for(user)
    request.env['omniauth.origin'] || stored_location_for(user) || users_path(user)
  end
end
```

目前沒有使用 devise 1.5 以上的原因是在這裡會遇到 redirect loop 的問題  

#### 結論

本篇範例主要以最常用的 facebook 登入為主  
另外也提供了整合 openid 至現有帳號的功能  
大多數的使用者帳號系統最在意的是能否拿到 email, 雖然 Facebook 是最多人使用的 open id, 但是基於網站本身的內容特性, 提供網站自己的註冊流程也是必要的  
接下來還有另一篇介紹上述範例中的 rspec 會怎麼寫  

