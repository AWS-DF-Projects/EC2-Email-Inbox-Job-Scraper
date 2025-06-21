## EC2-Email-Inbox-Job-Scraper

This version of the project was created after realising most of the jobs I'm actually interested in land in my email inbox not on random job sites. So instead of scraping public sites, this setup scrapes my own emails for job posts I’ve signed up to receive.

The goal is to automatically scan my inbox for new jobs, extract them, display them on a simple job board site, and send myself a daily email summary. That way I don't miss anything and don’t have to dig through endless job sites.

This is still in development, but the architecture is in place and the plan is clear.

![image](https://github.com/user-attachments/assets/0d932dba-252f-42ea-92c5-54c4ec9a330c)

Here’s how it works:
- A scheduled EventBridge rule triggers a Lambda function daily (e.g., 6AM UTC).
- The Lambda spins up an EC2 instance from a prebuilt AMI (which has Docker installed) and passes in the user data script.
- The EC2 user data script:
  - Starts Docker
  - Pulls the scraper image from Docker Hub
  - Runs the scraper
  - Saves results to an attached EBS volume
  - Uploads scraped job data to S3
  - Publishes an SNS message to send an email to the user
  - Shuts itself down to save cost
- The static website on S3 displays the scraped jobs under `/job-images` or similar path.
- An email is sent only if jobs are found, including a summary and a link to the site.

This version should better suit my needs because the email sources are already tailored to my interests things like LinkedIn and Indeed alerts, filtered job subscriptions, and so on.


