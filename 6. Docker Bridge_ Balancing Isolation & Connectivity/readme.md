Docker Bridge: Balancing Isolation & Connectivity
Objective
This exercise demonstrates network isolation in Docker containers. We will explore how containers within the same custom bridge network can communicate, while containers on different networks remain isolated. This is essential for securing microservices and containerized applications.

Introduction to Docker Networking
Docker networking is a critical feature that enables containers to communicate with each other, while ensuring security and isolation. Docker offers several types of networks, each suited to different use cases:

Types of Docker Networks:
Bridge Network (Default): Allows communication between containers using internal IPs unless restricted.

Custom Bridge Network: Offers more control and supports name-based resolution.

Host Network: Attaches containers directly to the host’s network stack.

Overlay Network: Enables communication across multiple hosts (in Docker Swarm mode).

Macvlan Network: Assigns a MAC address to each container, making them appear as separate devices on the network.

None Network: Completely disables networking for containers.

For this demonstration, we focus on the custom bridge network, which improves control and network isolation.

Why Use a Custom Bridge Network?
A custom bridge network offers several advantages:

✅ Improved Security: Containers on different networks are isolated by default.

✅ Better Performance: Containers can communicate directly without the overhead of using the host network stack.

✅ DNS-Based Resolution: Containers can communicate using names instead of IPs.

✅ Greater Control: You can define specific subnets, IP ranges, and gateways.

In this tutorial, we create a custom bridge network named Vedang-bridge and connect multiple containers.

Setup Instructions
1. Creating a Custom Bridge Network
To create a custom bridge network with specific subnets and IP ranges, run the following command:

docker network create --driver bridge --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 Vedang-bridge
Explanation:

--driver bridge: Uses the default bridge network mode.

--subnet 172.20.0.0/16: Defines the network's IP range.

--ip-range 172.20.240.0/20: Allocates IPs dynamically.

2. Running Containers in the Custom Network
Next, we will run two containers: a Redis container and a BusyBox container, both connected to the Vedang-bridge network.

Running the Redis Container (Vedang-database)
docker run -itd --net=Vedang-bridge --name=Vedang-database redis
Running the BusyBox Container (Vedang-server-A)
docker run -itd --net=Vedang-bridge --name=Vedang-server-A busybox
3. Checking Container IPs
Inspect the Vedang-bridge network to check the IP addresses of the containers:

docker network inspect Vedang-bridge
Expected Output:

Vedang-database: 172.20.240.1

Vedang-server-A: 172.20.240.2

4. Testing Communication Between Containers
Ping from Vedang-database to Vedang-server-A
docker exec -it Vedang-database ping 172.20.240.2
Ping from Vedang-server-A to Vedang-database
docker exec -it Vedang-server-A ping 172.20.240.1
Expected Outcome: Both containers should successfully ping each other, as they are on the same network.

5. Demonstrating Network Isolation with a Third Container
To show network isolation, let's add a third container, Vedang-server-B, to the default Docker bridge network.

docker run -itd --name=Vedang-server-B busybox
Get the IP of Vedang-server-B
docker inspect -format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' Vedang-server-B
Expected output: 172.17.0.2 (or another IP from the default bridge network).

6. Testing Communication Between Different Networks
Now, let's test communication between containers on different networks.

Ping from Vedang-database to Vedang-server-B
docker exec -it Vedang-database ping 172.17.0.2
Expected Outcome: The ping should fail, as the containers are on different networks.

7. Confirming Network Isolation
You can inspect both the Vedang-bridge and the default bridge networks to verify isolation:

docker network inspect Vedang-bridge
docker network inspect bridge
Expected Results:

Vedang-bridge should contain Vedang-database and Vedang-server-A.

bridge should contain Vedang-server-B.

Conclusion
Containers within the same network can communicate with each other.

Containers on different networks are isolated by default.

Docker’s networking model ensures security and separation unless explicitly connected.

Next Steps
Experiment with Docker overlay networks for multi-host communication.

Explore Macvlan networks for advanced use cases.

Learn about Docker Swarm and how it uses networking for service discovery.

Happy Coding! 🚀
This README file explains the entire experiment clearly and provides easy-to-follow steps for setting up Docker bridge networking.


