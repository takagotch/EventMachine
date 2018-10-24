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

```ruby
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

require 'rubygems'
require 'eventmachine'
class Echo < EventMachine::Connection
  def initailze(*args)
    super
  end
  def receive_data(data)
    p data
    send_data data
    close_connection_after_writing
  end
  def unbind
    p ' connection totally closed'
  end
end
EventMachine.run{
  EventMachine.conect '127.0.0.1', 8081, Echo
}

require 'rubygems'
require 'eventmachine'
class Echo < EM::Connection
  attr_reader :queue
  def initialize(q)
    @queue = q
    cb = Proc.new do |msg|
      send_data(msg)
      q.pop &cb
    end
    q.pop &cb
  end
  def post_init
    send_data('Hello')
  end
  def receive_data(data)
    p data
  end  
end
class KeyboardHandler < EM::Connection
  include EM::Protocols::LineText2
  attr_reader :queue
  def initialize(q)
    @queue = q
  end
  def receive_line(data)
    @queue.push(data)
  end
end
EM.run {
  q = EM::Queue.new
  EM.connect('127.0.0.1', 8081, Echo, q)
  EM.open_keyboard(KeyboadHandler, q)
}

class Server
  attr_accessor :connections
  def initialze
    @connections = []
  end
  def start
    @signature = EventMachine.start_server('0.0.0.0', 3000, Connection) do |con|
      con.server = self
    end
  end
  def stop
    EventMachine.stop_server(@signature)
    unless wait_for_connections_and_stop
      EventMachine.add_periodic_timer(1) { wait_for_connections_and_stop }
    end
  end
  def wait_for_connections_and_stop
    if @connections.empty?
      EventMachine.stop
      true
    else
      puts "Waiting for #{@connections.size} connections(s) to finish ..."
      false
    end
  end
end
class Connection < EventMachine::Connection
  attr_accessor :server
  def unbind
    server.connection.delete(self)
  end
end
EventMachine::run{
  s = Server.new
  s.start
  puts "New server listening"
}

require 'socket'
EM.run{
  srvr = EM.start_server "0.0.0.0", 0
  p Socket.unpack_sockaddr_in( EM.get_sockname( srvr ) )
}

port, *ip_parts = get_peername[2,6].unpack "nC4"
ip = ip_parts.join('.')

require 'socket'
port, ip = Socket.unpack_sockaddr_in(get_peername)


$eventmachine_library = :pure_ruby
require 'eventmachine'
gem_original_require 'serialport'
require 'smsrelay/gsmpdu'
module EventMachine
  class EvmaSerialPort < StreamObject
    def self.open(dev, baud, databits, stopbits, parity)
      io = SerialPort.new(dev, baud, databits, stopbits, parity)
      return(EvmaSerialPort.new(io))
    end
    def initializer(io)
      super
    end
  end
  def eventable_read
    @last_activity = Reactor.instance.current_loop_time
    begin
      if io.respond_to?(:read_nonblock)
        10.times {
          data = io.read_nonblock(4096)
          EventMachine::event_callback uuid, ConnectionData, data
        }
      else
        data = io.sysread(4096)
        EventMachine::event_callback uuid, ConnectionData, data
      end
    rescue Error::EAGAIN, Errno::EWOULDBLOCK, EOFError
    rescue Errno::ECONNRESET, Errno::ECONNREFUSED
      @close_scheduled = true
      EventMachine::event_callback uuid, ConnectionUnbound, nil
    end
  end
end
class << self
end



```









