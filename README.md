# Portfolio Website

A modern, responsive, and fully static personal portfolio website with a dark theme and neon accents. Built to highlight cloud engineering and data science skills.

This guide outlines how to deploy this static portfolio website on an Amazon Web Services (AWS) EC2 instance using the Nginx web server.

## Architecture

- **AWS EC2**: Hosts the Linux server.
- **Nginx**: Lightweight, high-performance web server handling HTTP requests.
- **ALB (Application Load Balancer)**: Distributes incoming traffic across EC2 instances for high availability (Optional but recommended).

## Steps for Deployment

### 1. Launch an EC2 Instance
1. Log into your AWS Management Console.
2. Navigate to **EC2** and click **Launch Instance**.
3. Choose **Ubuntu Server 22.04 LTS** (or 24.04 LTS).
4. Select an instance type (e.g., `t2.micro` or `t3.micro` for free tier).
5. Choose or create a Key Pair for SSH access.
6. Under **Network settings**, allow **HTTP (80)** and **HTTPS (443)** traffic from the internet.
7. Launch the instance.

### 2. Connect to Your EC2 Instance
SSH into your newly created instance using your terminal:
```bash
ssh -i /path/to/your-key.pem ubuntu@<your-ec2-public-ip>
```

### 3. Install Nginx
Update package lists and install Nginx:
```bash
sudo apt update -y
sudo apt install nginx -y
```

### 4. Start and Enable Nginx
Ensure Nginx is running and configured to start on boot:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```
At this point, you should be able to view the default Nginx welcome page by visiting your EC2's public IP address in a browser.

### 5. Upload Website Files
Copy the contents of this repository to the default Nginx HTML folder (`/var/www/html/`).

From your local machine, where the files are stored:
```bash
scp -i /path/to/your-key.pem -r * ubuntu@<your-ec2-public-ip>:/home/ubuntu/
```

Then, on the EC2 instance, move the files to Nginx directory:
```bash
sudo cp -r /home/ubuntu/* /var/www/html/
```
*(Make sure `index.html` replaces the default Nginx `index.nginx-debian.html` file)*

### 6. Set Proper Permissions
Make sure Nginx has the right access to serve these files:
```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

### 7. Test the Deployment
Open your browser and navigate to `<your-ec2-public-ip>`. You should now see your modern, fully responsive portfolio website!

## Adding an Application Load Balancer (ALB)
To increase fault tolerance:
1. Go to **EC2 > Load Balancers** and click **Create Load Balancer**.
2. Select **Application Load Balancer**.
3. Configure it to listen on HTTP (Port 80).
4. Select two or more Availability Zones in your VPC.
5. Create a **Target Group** (type: Instance) and register your EC2 instance into the target group.
6. Once the ALB is active, point your domain's DNS `A Record` to the ALB's DNS name using **Route 53**.
