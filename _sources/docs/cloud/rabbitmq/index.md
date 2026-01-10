# RabbitMQ

```{toctree}
:maxdepth: 4
:hidden:
:caption: Topics

docs/installation
docs/projects
docs/exchange
docs/exchange-direct
docs/exchange-fanout
docs/exchange-topic
docs/exchange-headers

```

[Official Website](https://www.rabbitmq.com/)

- [YT Video 1 - Amigoscode](https://youtu.be/nFxjaVmFj5E?si=q_nRRVLuKzO1icIo)
- [YT Video 2 - JavaTechie](https://youtu.be/o4qCdBR4gUM?si=h1oPU1uGIa8OlB_v)

```yml
version: "3.9"

services:
  rabbitmq:
    image: rabbitmq:3.13-management
    container_name: rabbitmq
    ports:
      - "5672:5672" # AMQP
      - "15672:15672" # Management UI
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: admin
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    networks:
      - rabbitmq_network

volumes:
  rabbitmq_data:

networks:
  rabbitmq_network:
```

Udemy Course:

- [Course Link](https://www.udemy.com/course/rabbitmq-in-practice)
- [GitHub Link](https://github.com/bigdotsoftware/RabbitMQ-In-Practice)
