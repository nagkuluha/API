api_integration_app/
│
├── app.py                # Main application file
├── templates/            # Folder for HTML templates
│   ├── index.html        # Home page template
│   └── map.html          # Map display page template
├── .gitignore            # Git ignore file (to avoid uploading sensitive data)
└── requirements.txt      # File for required dependencies


import tweepy
import sendgrid
from sendgrid.helpers.mail import Mail, Email, To, Content
from flask import Flask, render_template, request, redirect, url_for
import requests

# Initialize Flask app
app = Flask(__name__)

# Set your API keys and secrets
TWITTER_API_KEY = 'your_twitter_api_key'
TWITTER_API_SECRET_KEY = 'your_twitter_api_secret_key'
TWITTER_ACCESS_TOKEN = 'your_twitter_access_token'
TWITTER_ACCESS_TOKEN_SECRET = 'your_twitter_access_token_secret'

GOOGLE_MAPS_API_KEY = 'your_google_maps_api_key'
SENDGRID_API_KEY = 'your_sendgrid_api_key'

# Twitter Authentication
auth = tweepy.OAuth1UserHandler(
    consumer_key=TWITTER_API_KEY,
    consumer_secret=TWITTER_API_SECRET_KEY,
    access_token=TWITTER_ACCESS_TOKEN,
    access_token_secret=TWITTER_ACCESS_TOKEN_SECRET
)
twitter_api = tweepy.API(auth)

# SendGrid Client
sg = sendgrid.SendGridAPIClient(api_key=SENDGRID_API_KEY)

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/post_tweet', methods=['POST'])
def post_tweet():
    tweet = request.form['tweet']
    try:
        # Post a tweet
        twitter_api.update_status(tweet)
        return redirect(url_for('home'))
    except tweepy.TweepError as e:
        return f"Error: {e}"

@app.route('/show_map', methods=['GET'])
def show_map():
    location = request.args.get('location', '')
    map_url = f"https://maps.googleapis.com/maps/api/staticmap?center={location}&zoom=14&size=400x400&key={GOOGLE_MAPS_API_KEY}"
    return render_template('map.html', map_url=map_url)

@app.route('/send_email', methods=['POST'])
def send_email():
    email_content = request.form['email_content']
    from_email = Email("your-email@example.com")
    to_email = To("recipient@example.com")
    subject = "Test Email from SendGrid"
    content = Content("text/plain", email_content)
    mail = Mail(from_email, to_email, subject, content)

    try:
        response = sg.send(mail)
        if response.status_code == 202:
            return redirect(url_for('home'))
        return f"Failed to send email. Status Code: {response.status_code}"
    except Exception as e:
        return str(e)

if __name__ == "__main__":
    app.run(debug=True)


<!DOCTYPE html>
<html>
<head>
    <title>API Integration App</title>
</head>
<body>
    <h1>API Integration App</h1>
    <form action="/post_tweet" method="POST">
        <input type="text" name="tweet" placeholder="Enter tweet" required>
        <button type="submit">Post Tweet</button>
    </form>

    <form action="/send_email" method="POST">
        <textarea name="email_content" placeholder="Enter email content" required></textarea>
        <button type="submit">Send Email</button>
    </form>

    <form action="/show_map" method="GET">
        <input type="text" name="location" placeholder="Enter location" required>
        <button type="submit">Show Map</button>
    </form>
</body>
</html>


<!DOCTYPE html>
<html>
<head>
    <title>Map Display</title>
</head>
<body>
    <h1>Map</h1>
    <img src="{{ map_url }}" alt="Map">
    <br><br>
    <a href="/">Back to Home</a>
</body>
</html>


# Python bytecode
__pycache__/
*.pyc
*.pyo

# Virtual environment
venv/
.env/

# API Keys (Add the path to a .env or other files that store sensitive keys)
*.env

# IDE files
.vscode/
.idea/


Flask==2.1.1
tweepy==4.9.0
sendgrid==6.9.1
requests==2.26.0


git clone https://github.com/your_username/api_integration_app.git
cd api_integration_app
pip install -r requirements.txt
python app.py

Web Browser Access: http://127.0.0.1:5000/
