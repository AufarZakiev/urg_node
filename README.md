# urg_node

## Overview

ROS wrapper for the Hokuyo urg_c library with sending data via UDP using custom compression to make it possible to send without [packet fragmentation](https://ru.wikipedia.org/wiki/UDP#%D0%94%D0%BB%D0%B8%D0%BD%D0%B0_%D0%B4%D0%B0%D1%82%D0%B0%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D1%8B).

## How to use:

Launch this on Engineer. This will start the UDP server waiting for client to connect. Only one client is supported on the same time.

```sh
roslaunch udp_urg_node usp_urg_lidar.launch
```