# AliyunOpenSearch

对Aliyun Open Search API 进行封装.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'aliyun_open_search'

# or

gem 'aliyun_open_search', github: "LcpMarvel/aliyun_open_search"

```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install aliyun_open_search

## Usage

### 准备工作
#### 定义 3 个环境变量

```ruby

ACCESS_KEY_ID: "your access_key_id"
ACCESS_KEY_SECRET: "your access_key_secret"

OPEN_SEARCH_HOST: "http://opensearch-cn-hangzhou.aliyuncs.com"

```

### 数据处理API

```ruby
# app_name 是阿里云 应用名称
# params 的内容按照阿里云文档中的约定组织
require "aliyun_open_search"

params =  {
            action: :push,
            table_name: :cars,
            items: [
              {
                "cmd": "update",
                "timestamp": 1_420_070_400_010,
                "fields": {
                  "id": "121139313135",
                  "styl_name": "aodi",
                  "acquirer_id": "1"
                }
              },
              {
                "cmd": "update",
                "timestamp": 1_420_070_400_020,
                "fields": {
                  "id": "1211391233136",
                  "styl_name": "baoma",
                  "acquirer_id": "1"
                }
              }
            ]
          }

response = AliyunOpenSearch::Syncs.new(app_name).execute(params)
response_body = JSON.load(response.body)

```

### 搜索

```ruby
require "aliyun_open_search"

# index_names 是阿里云 应用名称, 可以多个
# params 的内容按照阿里云文档中的约定组织

params =  {
            query: "config=start:0,hit:10,format=fulljson&&query=default:'的'&&filter=create_timestamp>1423000000&&sort=+type;-RANK&fetch_fields=id;title;body;url;type;create_timestamp",
            first_formula_name: "first_bbs",
            formula: "second_bbs",
            summary: "summary_snipped:1,summary_field:title,summary_element:high,summary_len:32,summary_ellipsis:...;summary_snipped:2,summary_field:body,summary_element:high,summary_len:60,summary_ellipsis:..."
          }

AliyunOpenSearch::Search.new("test", "test2", "test3").execute(params)

```

### 签名
##### 你可以直接使用封装好的方法进行签名(文档读得好累)

```ruby
  
  AliyunOpenSearch.request_method = "POST" # 默认是 GET, 大写

  # custom_params 的内容按照阿里云文档中的约定组织
  custom_params = {
    "action" => "push",
    "table_name" => "cars",
    "items" => [
      {
        "cmd": "update",
        "timestamp": 1_420_070_400_010,
        "fields": {
          "id": "121139313135",
          "styl_name": "aodi",
          "acquirer_id": "1"
        }
      }
    ].to_json
  }

  AliyunOpenSearch::Base.signature(
    AliyunOpenSearch::Base.new.basic_params.merge!(custom_params)
  )

```


## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake rspec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/aliyun_open_search. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

