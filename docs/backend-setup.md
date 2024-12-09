# LeaderPort Back-end Setup Guide
This is a rough draft of a setup guide for developers that will be using LeaderPort. Most of the details are focused on the back-end setup, since the front-end install of the npm package will be pretty straightforward. I've included most of the common stack setups, but feel free to create an issue or pull request to add more.    

## Prerequisites
Before starting any of the setup options, ensure you have:
1. Install Bun:
   ```bash
   curl -fsSL https://bun.sh/install | bash
   ```
2. Verify installation:
   ```bash
   bun --version
   ```

## Back-end Setup
Details on setting up the infrastructure to use LeaderPort effectively on the major cloud providers or self-hosted solutions.

### Option 1: AWS with Valkey

1. Set up an AWS account if you don't have one.

2. Create an ElastiCache for Redis cluster:
   - Log in to AWS Console
   - Navigate to ElastiCache
   - Create a new Redis cluster
   - Note the cluster endpoint

3. Set up IAM roles and security groups:
   - Create an IAM role for ElastiCache access
   - Set up security groups to allow inbound traffic on port 6379

4. Deploy the LeaderPort backend:
   - Clone the LeaderPort backend repository
   - Install dependencies:
     ```bash
     bun install
     ```
   - Update configuration with your ElastiCache endpoint
   - Initialize Drizzle:
     ```bash
     bun add -d drizzle-kit
     bun drizzle-kit push:pg
     ```
   - Deploy to AWS (e.g., using Elastic Beanstalk or ECS)
   - Start the Hono server:
     ```bash
     bun run start
     ```

### Option 2: AWS with Valkey on EC2 with Graviton Processors

AWS Graviton processors offer excellent price performance for cloud workloads. Here's how to set up LeaderPort using AWS EC2 instances powered by Graviton:

1. Sign in to the AWS Management Console:
   - Go to [aws.amazon.com](https://aws.amazon.com) and log in to your account.

2. Launch an EC2 instance with Graviton:
   - Navigate to EC2 in the AWS Console.
   - Click "Launch Instance".
   - In the instance type selection, look for Graviton-based options like M6g, C6g, or R6g, depending on your workload needs.
   - Choose an Amazon Linux 2 AMI optimized for Graviton.

3. Configure your instance:
   - Set up security groups to allow necessary inbound traffic.
   - Choose or create a key pair for SSH access.

4. Install and configure backend:
   ```bash
   # Install Bun
   curl -fsSL https://bun.sh/install | bash
   
   # Install dependencies
   bun install
   
   # Setup database with Drizzle
   bun add -d drizzle-kit
   bun drizzle-kit push:pg
   ```

5. Install and configure Valkey:
   - SSH into your instance
   - Install Valkey (replace with actual Valkey installation commands)
   - Configure Valkey to start on boot

6. Deploy LeaderPort backend:
   - Update your backend configuration with the Graviton instance details
   - Start the Hono server:
     ```bash
     bun run start
     ```

7. Connect your frontend:
   - Update your frontend configuration with the backend API endpoint

8. Test the connection:
   - Use appropriate tools to interact with your Valkey instance and verify functionality.

Benefits of using AWS Graviton for LeaderPort:
- Up to 40% better price performance compared to x86-based instances.
- Reduced energy consumption, supporting sustainability goals.
- Seamless integration with other AWS services.
- Support for a wide range of operating systems and software.

For more information on optimizing your application for Graviton, refer to the [AWS Graviton Getting Started Guide](https://aws.amazon.com/ec2/graviton/getting-started/).


### Option 3: Google Cloud Platform with Valkey

Google Cloud Platform offers a flexible infrastructure that can be used to host Valkey, providing a robust solution for your LeaderPort backend. Here's how to set it up:

1. Set up a Google Cloud Platform account:
   - Go to [cloud.google.com](https://cloud.google.com) and create an account if you don't have one.
   - Create a new project for your LeaderPort application.

2. Create a Compute Engine instance for Valkey:
   - Navigate to the Compute Engine page in the GCP Console.
   - Click "Create Instance".
   - Choose an appropriate machine type (e.g., e2-medium for initial testing).
   - Select your preferred region and zone.
   - Choose an operating system (e.g., Ubuntu 20.04 LTS).
   - Allow HTTP and HTTPS traffic in the firewall settings.
   - Click "Create" to provision your instance.

3. Set up Valkey on the instance:
   - SSH into your new instance from the GCP Console.
   - Update the system: `sudo apt-get update && sudo apt-get upgrade -y`
   - Install Valkey (replace with actual installation commands for Valkey):
     ```bash
     # Example commands (adjust based on Valkey's actual installation process)
     wget https://github.com/valkey/releases/download/v1.0.0/valkey-server
     chmod +x valkey-server
     sudo mv valkey-server /usr/local/bin/
     ```
   - Configure Valkey to start on boot (create a systemd service file)

4. Set up backend on the instance:
   ```bash
   # Install Bun
   curl -fsSL https://bun.sh/install | bash
   
   # Install dependencies
   bun install
   
   # Setup database
   bun add -d drizzle-kit
   bun drizzle-kit push:pg
   ```

5. Set up networking:
   - In the VPC network settings, create firewall rules to allow incoming traffic on:
     - Port 6379 (Valkey)
     - Port 3000 (Hono server)

6. Note the connection details:
   - Once your instance is set up, note down the external IP address

7. Deploy the LeaderPort backend:
   - Clone the LeaderPort backend repository
   - Update the configuration file with your Valkey instance's IP address and port
   - Deploy your backend to Google Cloud (e.g., using Google Kubernetes Engine or Cloud Run)

8. Connect your frontend:
   - Update your frontend configuration with the backend API endpoint

9. Test the connection:
   - Submit a test score and retrieve the leaderboard to ensure everything is working correctly.

Benefits of using GCP with Valkey for LeaderPort:
- Full control over your Valkey instance for custom configurations.
- Ability to scale your Valkey instance as needed.
- Integration with other GCP services for comprehensive application deployment.
- Robust networking and security options provided by GCP.
- Cost-effective solution, especially for smaller to medium-sized applications.


### Option 4: DigitalOcean with Valkey 1-Click App

DigitalOcean offers a Valkey 1-Click App, which provides a pre-configured Valkey instance that's compatible with LeaderPort. This option simplifies the setup process significantly.

1. Create a DigitalOcean account:
   - Go to [digitalocean.com](https://www.digitalocean.com) and sign up if you haven't already.

2. Deploy Valkey 1-Click App:
   - In the DigitalOcean control panel, go to "Marketplace".
   - Search for "Valkey" and select the Valkey 1-Click App.
   - Click "Create Valkey Droplet".
   - Choose your preferred Droplet size (start with Basic for testing).
   - Select a datacenter region close to your target audience.
   - Add SSH keys for secure access.
   - Click "Create Droplet" to provision your Valkey instance.

3. Access your Valkey instance:
   - Once created, note down the IP address of your Droplet.
   - SSH into your Droplet: `ssh root@your_droplet_ip`
   - The Valkey password can be found in `/root/.digitalocean_passwords`

4. Configure Valkey:
   - Valkey configuration file is located at `/srv/valkey/6379.conf`
   - By default, Valkey is bound to the Droplet IP address and localhost.

5. Firewall settings:
   - UFW firewall is enabled by default.
   - Only ports 22 (SSH) and 6379 (Valkey) are open.

6. Deploy LeaderPort backend:
   - Update your backend configuration with the Valkey connection details.
   - Deploy your backend to DigitalOcean (e.g., using another Droplet or App Platform).

7. Connect your frontend:
   - Update your frontend configuration with the backend API endpoint.

8. Test the connection:
   - Use `valkey-cli` to interact with your Valkey instance:
     ```
     valkey-cli
     valkey> ping
     PONG
     valkey> set valkyries
     OK
     valkey> get valkyries
     "bar"
     ```

For more detailed instructions and API usage, refer to the [official DigitalOcean Valkey documentation](https://docs.digitalocean.com/products/marketplace/catalog/valkey/).

Benefits of using DigitalOcean's Valkey 1-Click App with LeaderPort:
- Pre-configured and optimized Valkey instance.
- Simple deployment process.
- Built-in security measures with UFW firewall.
- Easy scalability with DigitalOcean's infrastructure.
- Cost-effective solution for startups and small to medium projects.

### Option 5: Oracle Cloud Infrastructure (OCI) with Valkey

Oracle Cloud Infrastructure now supports Valkey, offering a high-performance, scalable solution for your LeaderPort backend. Here's how to set it up:

1. Create an Oracle Cloud account:
   - Go to [cloud.oracle.com](https://cloud.oracle.com) and sign up if you haven't already.

2. Set up a Valkey instance:
   - In the OCI Console, navigate to the Marketplace.
   - Search for "Valkey" and select the Valkey image.
   - Choose your compartment, availability domain, and shape (VM.Standard.E4.Flex recommended for production).
   - Configure networking settings, ensuring proper security list rules.
   - Launch the instance.

3. Access your Valkey instance:
   - Once created, note down the public IP address of your instance.
   - SSH into your instance using the provided key.

4. Configure Valkey:
   - Valkey configuration can be found in the instance.
   - By default, Valkey is configured to use 70% of the instance's memory.

5. Deploy LeaderPort backend:
   - Update your backend configuration with the Valkey connection details.
   - Deploy your backend to OCI (e.g., using OCI Compute or Container Instances).

6. Connect your frontend:
   - Update your frontend configuration with the backend API endpoint.

7. Test the connection:
   - Use Valkey CLI or your application to interact with the Valkey instance.

Benefits of using Oracle Cloud Infrastructure for Valkey with LeaderPort:
- High-performance infrastructure with flexible VM shapes.
- Integration with OCI's comprehensive security features.
- Scalability to handle growing leaderboards and user bases.
- Potential cost savings with Oracle's competitive pricing.

For more detailed instructions and best practices, refer to the [official Oracle blog post on Valkey support](https://blogs.oracle.com/cloud-infrastructure/post/oracle-supports-valkey).


### Option 6: Self-hosted or Other Providers

Valkey is flexible and can work with any Redis-compatible service. If you're using a different cloud provider or want to self-host:

1. Set up a Redis instance:
   - Install and configure Redis on your own server, or
   - Use a Redis-compatible service from your preferred cloud provider.

2. Secure your Redis instance:
   - Ensure proper authentication is set up.
   - Configure firewall rules to restrict access.

3. Deploy the LeaderPort backend:
   - Clone the LeaderPort backend repository
   - Update configuration with your Redis connection details
   - Deploy your backend to your chosen infrastructure

For guidance on optimizing LeaderPort for your specific setup, please reach out to our support team.

## Connecting Front-end to Back-end

1. Update your frontend configuration with the backend API endpoint.

2. Test the connection by submitting a score and retrieving the leaderboard.

3. Celebrate 🎉

## Environment Configuration

Create a .env file in your project root:
```env
DATABASE_URL=postgres://...
VALKEY_URL=redis://localhost:6379
PORT=3000
NODE_ENV=development
```

## Verifying Your Setup

1. Test the Hono server:
   ```bash
   curl http://localhost:3000/health
   ```

2. Test Valkey connection:
   ```bash
   curl http://localhost:3000/api/leaderboard
   ```

3. Test WebSocket connection:
   ```bash
   websocat ws://localhost:3000/ws
   ```

For frontend connection details, see [Frontend Setup Guide](./frontend-setup.md).