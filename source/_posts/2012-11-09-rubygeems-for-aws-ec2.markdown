---
layout: post
title: "Rubygems 處理 Amazon Web Service (AWS) EC2"
date: 2012-11-09 13:55
comments: true
categories: [Rubygems, AWS, EC2]
---
參考: <a href="https://github.com/grempe/amazon-ec2" target="_blank">https://github.com/grempe/amazon-ec2</a>  

<!-- more -->

### 安裝設定

```ruby Gemfile
gem 'amazon-ec2', :require => "AWS"
```

```yaml config/aws.yml
development: &default
  :access_key_id: "abcdabcd"
  :secret_access_key: "abcdabcd"
  :host: "ap-southeast-1.ec2.amazonaws.com"
test:
  <<: *default
```

`access_key_id` 和 `secret_access_key` 可至 <a href="https://portal.aws.amazon.com/gp/aws/developer/account/index.html?action=access-key" target="_blank">AWS Security Credentials</a> 查看  
`host` 則是目標 region 所對應的 endpoint，對應表可至 <a href="https://github.com/garnaat/missingcloud/blob/master/aws.json#L372" target="_blank">https://github.com/garnaat/missingcloud/blob/master/aws.json#L372</a> 查看  

養成好習慣, 請勿將 yaml 檔 commit 進去...  

```ruby .gitignore
config/aws.yml
```

```yaml config/aws.yml.example
# to see region and host mapping :
#   https://github.com/garnaat/missingcloud/blob/master/aws.json#L372
development: &default
  :access_key_id: ""
  :secret_access_key: ""
  :host: ""
test:
  <<: *default
```

### 使用方法

* 初始化

```ruby example.rb
config = YAML::load(File.open("#{Rails.root}/config/aws.yml"))[Rails.env]
ec2 = AWS::EC2::Base.new(:access_key_id => config[:access_key_id], :secret_access_key => config[:secret_access_key], :server => config[:host])
```

* 取得 instance 列表

```ruby exmaple.rb
instances = ec2.describe_instances
# instances["reservationSet"]["item"] 才會取得 array
```

* 取得所有 snapshots (自己 create 的)

```ruby exmaple.rb
snapshots = ec2.describe_snapshots(:owner => "self")
# snapshots["snapshotSet"]["item"] 才會取得 array
```

* 取得所有 volumes

```ruby exmaple.rb
volumes = ec2.describe_volumes
# volumes["volumeSet"]["item"] 才會取得 array
```

* 建立 snapshot, 必須先取得 volume id

```ruby example.rb
volume_id = volumes["volumeSet"]["item"].first["volumeId"]
ec2.create_snapshot(:volume_id => volume_id, :description => "my snapshot")
```

* 建立某個 instance 的 snapshot, 有 instance id 的情況下

```ruby example.rb
instance_id = "i-abcd"
volume_id = (((ec2.describe_volumes["volumeSet"] || {})["item"] || []).select { |volume|
  (volume["attachmentSet"]["item"].select{ |ins| ins["instanceId"] == instance_id }.first || {})["instanceId"] == instance_id
}.first || {})["volumeId"]
result = ec2.create_snapshot(:volume_id => volume_id, :description => "foo bar")
puts result["snapshotId"] # 新的 snapshot id
```

* 更多可參考 API: <a href="https://github.com/grempe/amazon-ec2/tree/master/lib/AWS/EC2" target="_blank">https://github.com/grempe/amazon-ec2/tree/master/lib/AWS/EC2</a>

