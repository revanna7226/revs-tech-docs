# Installation

## In Windows

1. Download .exe/binary files of RabbitMQ and Erlang.
2. Install RabbitMQ and Erlang in Windows.
3. Set ERLANG_HOME = Erlang OTP Installation Path
4. Add Erlang bin path in Path.
5. Enable RabbitMQ Management UI (Very Important) the web-based dashboard:

   ```bash
   rabbitmq-plugins enable rabbitmq_management
   ```

6. Run `rabbitmq-server.bat` batch file.
7. Access RabbitMQ Web UI on [http://localhost:15672](http://localhost:15672).
   - Username: guest
   - Password: guest
8. Verify RabbitMQ Is Running
   Check ports
   Port -> Purpose
   5672 -> AMQP (applications)
   15672 -> Management UI

## In Linux
