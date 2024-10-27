import requests
from flask import Flask, request

app = Flask(__name__)

# Define the proxy server URL
proxy_url = "http://your-proxy-server.com:8080"

# Create a dictionary to store the blocked websites
blocked_websites = {}

# Define the Doge-themed HTML template
html_template = """
<html>
  <head>
    <title>Doge Unblocker</title>
    <style>
      body {
        background-color: #f2f2f2;
        font-family: Arial, sans-serif;
      }
      #doge {
        width: 200px;
        height: 200px;
        margin: 40px auto;
        background-image: url('https://i.imgur.com/lGf3z2B.jpg');
        background-size: cover;
        border-radius: 50%;
      }
    </style>
  </head>
  <body>
    <div id="doge"></div>
    <h1>Doge Unblocker</h1>
    <form action="/unblock" method="post">
      <input type="text" name="url" placeholder="Enter URL to unblock">
      <button type="submit">Unblock!</button>
    </form>
  </body>
</html>
"""

# Define the route for the main page
@app.route("/")
def index():
    return html_template

# Define the route for the unblock request
@app.route("/unblock", methods=["POST"])
def unblock():
    url = request.form["url"]
    if url not in blocked_websites:
        # Make a request to the proxy server to unblock the website
        proxy_response = requests.get(proxy_url, params={"url": url})
        if proxy_response.status_code == 200:
            # Store the unblocked website in the dictionary
            blocked_websites[url] = True
            return f" Successfully unblocked {url}!"
        else:
            return f"Failed to unblock {url}. Error: {proxy_response.text}"
    else:
        return f"{url} is already unblocked!"

if __name__ == "__main__":
    app.run(debug=True)
