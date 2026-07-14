# ROS 2 Python Publisher and Subscriber (Talker & Listener)

## Aim

To create and execute a simple **ROS 2 Python Publisher and Subscriber system** where:

* The **Talker node** publishes messages to a ROS 2 topic.
* The **Listener node** subscribes to the topic and receives messages.
* Communication is performed using the ROS 2 **Publisher–Subscriber architecture**.

The publisher sends `"Hello ROS 2: <count>"` messages every second, and the subscriber displays the received messages using the ROS 2 logging system.

---

## Prerequisites

* Ubuntu 24.04
* ROS 2 Jazzy Jalisco
* Python 3
* Colcon build tool

---

## Project Structure

```
ros_day2/
│
├── src/
│   └── listener_talker/
│       │
│       ├── listener_talker/
│       │   ├── talker.py
│       │   └── listener.py
│       │
│       ├── package.xml
│       ├── setup.py
│       └── setup.cfg
│
├── build/
├── install/
└── log/
```

---

# Publisher Node (Talker)

The **Talker node** creates a publisher that sends messages on the `chatter` topic.

## `talker.py`

```python
import rclpy
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node
from std_msgs.msg import String


class Talker(Node):
    def __init__(self):
        super().__init__('talker')
        self.publisher = self.create_publisher(String, 'chatter', 10)
        self.count = 0
        self.timer = self.create_timer(1.0, self.publish_message)

    def publish_message(self):
        message = String()
        message.data = f'Hello ROS 2: {self.count}'
        self.publisher.publish(message)
        self.get_logger().info(f'Publishing: "{message.data}"')
        self.count += 1


def main(args=None):
    rclpy.init(args=args)
    node = Talker()

    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        if rclpy.ok():
            rclpy.shutdown()


if __name__ == '__main__':
    main()
```

---

# Subscriber Node (Listener)

The **Listener node** subscribes to the `chatter` topic and prints the received messages.

## `listener.py`

```python
import rclpy
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node
from std_msgs.msg import String


class Listener(Node):
    def __init__(self):
        super().__init__('listener')
        self.subscription = self.create_subscription(
            String,
            'chatter',
            self.listener_callback,
            10,
        )

    def listener_callback(self, message):
        self.get_logger().info(f'I heard: "{message.data}"')


def main(args=None):
    rclpy.init(args=args)
    node = Listener()

    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        if rclpy.ok():
            rclpy.shutdown()


if __name__ == '__main__':
    main()
```

---

# Code Explanation

## Talker Node

* `rclpy` is imported to use the ROS 2 Python client library.
* `Node` is used to create a ROS 2 node.
* `String` message type is imported from `std_msgs`.
* A node named **talker** is created.
* A publisher is created on the **chatter** topic.
* A timer publishes a message every **1 second**.
* The message format is:

```
Hello ROS 2: <count>
```

* Each published message is displayed using the ROS 2 logger.

---

## Listener Node

* A node named **listener** is created.
* A subscriber is created for the **chatter** topic.
* When a message is received, the callback function executes.
* The received message is displayed using ROS 2 logging.

---

# Build Instructions

Navigate to the ROS 2 workspace:

```bash
cd ~/ros_day2
```

Build the package:

```bash
colcon build --symlink-install
```

### Build Output

```
Starting >>> listner_talker
Finished <<< listner_talker [0.97s]

Summary: 1 package finished [1.06s]
```

Source the workspace:

```bash
source install/setup.bash
```

---

# Running the Nodes

## Terminal 1: Run Talker Node

```bash
ros2 run listener_talker talker
```

or

```bash
/usr/bin/python3 /home/bnmit/ros_day2/src/listener_talker/listener_talker/talker.py
```

### Talker Output

```
[INFO] [1784018958.217572667] [talker]: Publishing: "Hello ROS 2: 0"
[INFO] [1784018959.207697954] [talker]: Publishing: "Hello ROS 2: 1"
[INFO] [1784018960.208555973] [talker]: Publishing: "Hello ROS 2: 2"
[INFO] [1784018961.206966893] [talker]: Publishing: "Hello ROS 2: 3"
[INFO] [1784018962.206933017] [talker]: Publishing: "Hello ROS 2: 4"
[INFO] [1784018963.207234905] [talker]: Publishing: "Hello ROS 2: 5"
[INFO] [1784018964.207335698] [talker]: Publishing: "Hello ROS 2: 6"
```

---

## Terminal 2: Run Listener Node

```bash
ros2 run listener_talker listener
```

or

```bash
/usr/bin/python3 /home/bnmit/ros_day2/src/listener_talker/listener_talker/listener.py
```

### Listener Output

```
[INFO] [1784018968.704852287] [listener]: I heard: "Hello ROS 2: 165"
[INFO] [1784018969.715730277] [listener]: I heard: "Hello ROS 2: 166"
[INFO] [1784018970.689714857] [listener]: I heard: "Hello ROS 2: 167"
[INFO] [1784018971.712596514] [listener]: I heard: "Hello ROS 2: 168"
[INFO] [1784018972.698309770] [listener]: I heard: "Hello ROS 2: 169"
[INFO] [1784018973.692302957] [listener]: I heard: "Hello ROS 2: 170"
```

---

# ROS 2 Communication Flow

```
              Publish
+---------+  ------------>  +-----------+
| Talker  |                |  chatter  |
|  Node   |                |  Topic    |
+---------+                +-----------+
                                  |
                                  |
                                  v
                         +---------------+
                         |    Listener   |
                         |     Node      |
                         +---------------+

```

---

# Conclusion

A ROS 2 Python **Publisher and Subscriber system** was successfully created and executed.

The **Talker node** continuously publishes messages to the `chatter` topic, and the **Listener node** receives and displays those messages. This project demonstrates the fundamental ROS 2 communication mechanism using topics and the publisher–subscriber model.