### EventMachine
---

https://github.com/eventmachine/eventmachine


```
gem install eventmachine
gem "eventmachine"

```

```ruby
require 'eventmachine'
module EchoServer
  def post_init
    puts "-- someone connected to the echo server!"
  end
  def receive_data data
    send_data ">>you sent: #{data}"
    close_connection if data =~ /quit/i
  end
  def unbind
    puts "-- someone disconnected from the echo server!"
  end
end
EventMachine.run {
  EventMachine.start_server "127.0.0.1", 8081, EchoServer
}

```

---

```
git clone https://github.com/eventmachine/eventmachine.git
cd eventmachine
bundle install --path vendor/bundle
bundle exec rake compile
bundle exec irb

apk add --no-cache g++ musl-dev make
apk add --no-cache libstdc++

rale compile -- --with-ssl-dir=c:/OpenSSL
bundle config build.eventmachine --with-ssl-dir=c:/OpenSSL
gem install eventmachine -- --with-ssl-dir=c:/OpenSSL
gem install eventmachine --platform ruby -- --with-ssl-dir=c:/OpenSSL


```

```
#!/usr/bin/env ruby
#clinet_1
require 'rubygems'
require 'eventmachine'

class Echo < EventMachine::Connection
  def post_init
    send_data 'Hello'
  end
  def receive_data(data)
    p data
  end
end
EventMachine.run {
  EventMachine::connect '127.0.0.1', 8081, Echo
}

```









