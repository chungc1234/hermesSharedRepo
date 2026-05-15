\---

name: send-email-via-sendgrid

description: "Sends email using the SendGrid API to bypass blocked SMTP ports."

version: 1.0.0

author: Ai

license: MIT

metadata:

&#x20;hermes:

&#x20;tags: \[Email, SendGrid, API, Cloud, SMTP]

prerequisites:

&#x20;environment\_variables:

&#x20;- SENDGRID\_API\_KEY

&#x20;- SENDGRID\_FROM\_EMAIL

&#x20;python\_packages:

&#x20;- sendgrid

\---

\# Send Email via SendGrid API

This skill provides a reliable method for sending emails from environments where standard SMTP ports (587, 465) are blocked, which is common in many cloud and containerized platforms like Railway. It uses the SendGrid API, which works over HTTPS.

\## 1. Prerequisites

Before using this skill, you must configure two environment variables and one Python package.

\### Environment Variables

The agent's environment needs the following variables set:

\- `SENDGRID\_API\_KEY`: Your API key from your SendGrid account.

\- `SENDGRID\_FROM\_EMAIL`: The verified sender email address you configured in SendGrid.

\### Python Package

The `sendgrid` library must be installed. If it's not present, the agent can install it:

bash

pip install sendgrid

\## 2. Usage

The process involves two tool calls:

1\. `write\_file`: Create a temporary Python script with the email content.

2\. `terminal`: Execute the script using a python interpreter that has the `sendgrid` library available.

This two-step process is necessary to work around potential sandbox restrictions of some code execution tools that may not have all the required libraries installed.

\### Example: Sending a "Hello World" Email

\#### Step A: Write the Script

Use the `write\_file` tool to create a script like this. Be sure to change the `to\_emails`, `subject`, and `plain\_text\_content` to your desired values.

python

import os

from sendgrid import SendGridAPIClient

from sendgrid.helpers.mail import Mail

\# Check for API key first to fail fast

api\_key = os.environ.get('SENDGRID\_API\_KEY')

from\_email = os.environ.get('SENDGRID\_FROM\_EMAIL')

if not api\_key:

&#x20;print("Error: SENDGRID\_API\_KEY environment variable not set.")

&#x20;exit(1)

if not from\_email:

&#x20;print("Error: SENDGRID\_FROM\_EMAIL environment variable not set.")

&#x20;exit(1)

message = Mail(

&#x20;from\_email=from\_email,

&#x20;to\_emails='recipient@example.com',

&#x20;subject='Hello from Hermes and SendGrid',

&#x20;plain\_text\_content='This is a test email sent via the SendGrid API.'

)

try:

&#x20;sendgrid\_client = SendGridAPIClient(api\_key)

&#x20;response = sendgrid\_client.send(message)

&#x20;if response.status\_code >= 200 and response.status\_code < 300:

&#x20;print("Email sent successfully!")

&#x20;else:

&#x20;print(f"Failed to send email. Status code: {response.status\_code}")

&#x20;print(response.body.decode('utf-8'))

except Exception as e:

&#x20;print(f"An error occurred: {e}")

\#### Step B: Execute the Script

Use the `terminal` tool to run the script you just created.

bash

python3 /tmp/send\_email.py

The output will confirm if the email was sent successfully.

