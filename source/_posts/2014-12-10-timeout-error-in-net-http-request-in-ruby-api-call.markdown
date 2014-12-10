---
layout: post
title: "Timeout error in Net::HTTP request in Ruby Api call"
date: 2014-12-10 13:33:25 +0800
comments: true
categories: 
---
I have a api call in Ruby on Rails application, and this call needs a little longer time, about 1min (60s). And I have this error:
```
[127.0.0.1] [PID-04824] execution expired
[127.0.0.1] [PID-04824] /opt/converse/ruby/lib/ruby/1.8/timeout.rb:64:in `rbuf_fill'
/opt/converse/ruby/lib/ruby/1.8/net/protocol.rb:134:in `rbuf_fill'
/opt/converse/ruby/lib/ruby/1.8/net/protocol.rb:116:in `readuntil'
/opt/converse/ruby/lib/ruby/1.8/net/protocol.rb:126:in `readline'
/opt/converse/ruby/lib/ruby/1.8/net/http.rb:2028:in `read_status_line'
/opt/converse/ruby/lib/ruby/1.8/net/http.rb:2017:in `read_new'
/opt/converse/ruby/lib/ruby/1.8/net/http.rb:1051:in `request'
/opt/converse/supadmin/releases/20141118030932/lib/rpm_api_proxy.rb:25:in `network_call'
/opt/converse/ruby/lib/ruby/1.8/net/http.rb:543:in `start'
/opt/converse/supadmin/releases/20141118030932/lib/rpm_api_proxy.rb:22:in `network_call'
/opt/converse/ruby/lib/ruby/1.8/timeout.rb:67:in `timeout'
/opt/converse/supadmin/releases/20141118030932/lib/rpm_api_proxy.rb:21:in `network_call'
/opt/converse/supadmin/releases/20141118030932/app/models/network.rb:399:in `enable_rpm'
```
And my implementation for HTTP request in API call is:
```
	http_client = Net::HTTP.new(uri.host, uri.port)
    http_client.verify_mode = OpenSSL::SSL::VERIFY_NONE
    http_client.use_ssl = true
    
    timeout = 240
    begin
      Timeout::timeout(timeout) do
        http_client.start do |http|
          req = Net::HTTP::Post.new(url)
          req.form_data = params
          resp = http.request(req)
          Rails.logger.info("resp: #{resp.body}")
          Rails.logger.info("rpm network call END")
          JSON.parse(resp.body)
        end
      end
    ensure
      http_client.finish rescue nil
    end
```
And the distination needs more than 43s to handle this request, so I'll get the Timeout error.

After searching in the web, I have found [this post](http://userprimary.net/posts/2008/04/13/setting-a-longer-timeout-for-nethttp-requests-in-ruby/) is very useful, and fixed my problem based on the solution.

```
    http_client = Net::HTTP.new(uri.host, uri.port)
    http_client.verify_mode = OpenSSL::SSL::VERIFY_NONE
    http_client.use_ssl = true
    
    timeout = 240
    http_client.read_timeout = timeout
    
    begin
      Timeout::timeout(timeout) do
        http_client.start do |http|
          req = Net::HTTP::Post.new(url)
          req.form_data = params
          resp = http.request(req)
          Rails.logger.info("resp: #{resp.body}")
          Rails.logger.info("rpm network call END")
          JSON.parse(resp.body)
        end
      end
    ensure
      http_client.finish rescue nil
    end
```
We need to set **read_timeout** for **Net::HTTP** object, because the default **read_timeout** is **60s**. So in my code, the block has set 240s for timeout using **Timeout::timeout(timeout)**, but the **http_client**(in the block) object's timeout time is **60s, the default value**. So I need add the below code to slove this problem.
```
http_client.read_timeout = timeout // 240s
```
**Reference:**[Setting a longer timeout for Net::HTTP Requests in Ruby](http://userprimary.net/posts/2008/04/13/setting-a-longer-timeout-for-nethttp-requests-in-ruby/)