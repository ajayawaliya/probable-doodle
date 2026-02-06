# probable-doodle

import requests
from bs4 import BeautifulSoup
import smtplib
import time

# Your Amazon product URL
URL = "https://www.amazon.com/dp/B08N5WRWNW"

# Your Amazon headers (to mimic a browser)
headers = {
    "User-Agent": "Your user-agent here"
}

# Target price
TARGET_PRICE = 300.00

# Email configuration
EMAIL = "youremail@gmail.com"
PASSWORD = "yourpassword"
TO_EMAIL = "recipient@gmail.com"

def check_price():
    response = requests.get(URL, headers=headers)
    soup = BeautifulSoup(response.content, 'html.parser')
    title = soup.find(id="productTitle").get_text().strip()
    price = soup.find(id="priceblock_ourprice").get_text()
    price = float(price.replace("$", "").replace(",", ""))
    
    if price < TARGET_PRICE:
        send_email(title, price)

def send_email(product, price):
    subject = f"Price Drop Alert: {product}"
    body = f"The price is now ${price}.\nCheck the link: {URL}"
    message = f"Subject: {subject}\n\n{body}"
    
    with smtplib.SMTP('smtp.gmail.com', 587) as server:
        server.starttls()
        server.login(EMAIL, PASSWORD)
        server.sendmail(EMAIL, TO_EMAIL, message)
    print("Email sent!")

# Check every 12 hours
while True:
    check_price()
    time.sleep(43200)
