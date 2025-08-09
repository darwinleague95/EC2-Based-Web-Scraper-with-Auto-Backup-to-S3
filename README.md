# EC2-Based-Web-Scraper-with-Auto-Backup-to-S3
🎯 Objective:
Create an EC2 instance that runs a Python web scraper on a schedule. The scraped data is saved locally and automatically backed up to an S3 bucket.

🧩 Steps to Complete the Project:
1️⃣ Create an S3 Bucket
Bucket Name: web-scraper-backup-<your-name>

Enable versioning

Set default encryption (SSE-S3 or SSE-KMS)

2️⃣ Launch EC2 Instance
OS: Amazon Linux 2 / Ubuntu

Instance type: t2.micro (Free Tier eligible)

Enable SSH access (port 22)

Allow HTTP if needed (for testing server output)

Create or use existing key pair (for SSH access)

3️⃣ SSH into EC2
bash
Copy
Edit
ssh -i your-key.pem ec2-user@<Public-IP>
4️⃣ Install Required Packages
bash
Copy
Edit
sudo yum update -y  # (or apt update for Ubuntu)
sudo yum install python3 pip git -y
pip3 install requests beautifulsoup4 boto3
5️⃣ Write the Python Web Scraper
Example: Scrape headlines from a website like Hacker News.

python
Copy
Edit
# webscraper.py
import requests
from bs4 import BeautifulSoup
import datetime

url = 'https://news.ycombinator.com'
response = requests.get(url)
soup = BeautifulSoup(response.text, 'html.parser')

headlines = soup.select('.titleline')
now = datetime.datetime.now().strftime('%Y-%m-%d-%H-%M')

with open(f'hn-headlines-{now}.txt', 'w') as f:
    for line in headlines:
        f.write(line.text + '\n')
6️⃣ Configure IAM Role for EC2
Attach a role with AmazonS3FullAccess to your EC2 instance

This allows your script to upload to S3

7️⃣ Upload Scraped Data to S3
Add to your script:

python
Copy
Edit
import boto3

s3 = boto3.client('s3')
bucket = 'web-scraper-backup-darwin'
filename = f'hn-headlines-{now}.txt'

s3.upload_file(filename, bucket, filename)
8️⃣ Set Up a Cron Job to Run It Automatically
bash
Copy
Edit
crontab -e
# Add this line to run every hour
0 * * * * /usr/bin/python3 /home/ec2-user/webscraper.py
9️⃣ Validate Output
Check S3 bucket for uploaded text files.

Monitor EC2 CPU usage & logs.

✅ Bonus:
Send a notification using SNS after each successful scrape.

Use CloudWatch to trigger alerts if scraping fails
