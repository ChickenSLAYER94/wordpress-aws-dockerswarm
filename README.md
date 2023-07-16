# WordPress Application on AWS using Docker Swarm

This project aims to deploy a WordPress application on AWS using Docker Swarm. Docker Swarm is a powerful container orchestration tool that allows for the seamless deployment and management of containerized applications at scale. When combined with AWS services, it creates a robust and scalable environment for hosting WordPress applications.

## Highlight

The following information serves as a comprehensive guide to deploying the WordPress application and showcases the key benefits of utilizing Docker Swarm and cloud architecture. These benefits include:

- Improved efficiency
- Scalability
- High availability
- Security and encryption
- Proper resource utilization
- Load balancing

## Deployment Steps

1. **AWS EC2 Instance Setup**: Create multiple instances (nodes) to run the application, ensuring that "ALLOW SSH traffic" and "Allow HTTP traffic from the internet" are enabled in the Network settings during instance creation.

2. **Install Docker**: Install Docker on all instances using the following commands:

    ```bash
    curl -fsSL https://get.docker.com -o get-docker.sh
    sh get-docker.sh
    ```

3. **Initiate Docker Swarm**: Connect to the manager node and use the following command to initialize Docker Swarm:

    ```bash
    sudo docker swarm init --advertise-addr <ip-address-of-node>
    ```

    Note: The output will provide a "docker swarm join" command that should be executed on the worker nodes. Prior to that, ensure that port 2377 is allowed inbound on the manager node's security group.

4. **Create Worker Nodes**: Connect to each worker node separately and execute the "docker swarm join" command obtained from the manager node.

5. **Check Node Status**: Use the following command on the manager node to verify the status of all nodes:

    ```bash
    sudo docker node ls
    ```

6. **Secure the Application with Secrets and Network Encryption**: Implement secrets and network encryption to enhance application security. To use secrets in Docker Compose, modify the docker-compose file as follows:
<!---
...
MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
MYSQL_DATABASE: wordpress
MYSQL_USER: wordpress
MYSQL_PASSWORD_FILE: /run/secrets/db_password
secrets:
  - db_root_password
  - db_password
...
--->

Secrets in Docker Compose are used to securely store passwords. Two files should be created in the manager node with the names `db_password.txt` and `db_root_password.txt` that hold the passwords. 
Creating an encrypted network in the manager node:
Use the command:
```bash
sudo docker network create --opt encrypted -d overlay my-wordpress-network
```
Using an externally created encrypted network "my-wordpress-network":
In the docker-compose file, add the following:
Code portion in yml file:
```
...
networks:
  my-wordpress-network:
    name: my-wordpress-network
    external: true
...
```
7. **Limit Memory Usage**: Efficiently manage resources by limiting memory and CPU usage in the worker nodes. Adjust the resource limits within the docker-compose-swarm.yml file.

8. **Database Replication**: Replicate the database across multiple instances by configuring the appropriate settings in the docker-compose-swarm.yml file.

9. **Stack Deployment**: Deploy the stack using the following command on the manager node:

    ```bash
    sudo docker stack deploy -c docker-compose-swarm.yml wpapp
    ```

> Note: "wpapp" is the chosen stack name, which can be modified as per your preference.

10. **Accessing WordPress**: After successful stack deployment, WordPress can be accessed by adding the appropriate inbound rule for port 8080 on all EC2 instances. Open a browser and enter either `http://<Public IPv4 address of any node/instance>:8080/` or `http://<Public IPv4 DNS of any node/instance>:8080` to access the WordPress application.

## Load Balancing

To enable load balancing for the WordPress application, follow these steps:

1. **Create Target Group**: Create a target group that includes all the instances to be balanced. This can be done through the EC2 console.

2. **Create Application Load Balancer**: Use the EC2 console to create an Application Load Balancer. Make sure to create a subnet group that allows port 8080.

3. **Configure Load Balancer**: Select the appropriate security group and configure the listener and routing rules for the load balancer, ensuring it targets the previously created target group.

4. **Access the Load Balanced Application**: Once the load balancer is created, access the WordPress application by entering `http://<dns-name-of-load-balancer>:8080` in a browser. The load balancer will effectively distribute traffic across the multiple EC2 instances.
> Note: Application load balancer is used here for load balancing because it allows to monitor health and helps to manage DNS.

By following these steps, you will successfully deploy a WordPress application on AWS using Docker Swarm. 
