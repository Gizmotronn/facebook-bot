# Facebook Messenger Bot --> DataCamp.com

Tutorial link: https://www.datacamp.com/community/tutorials/facebook-chatbot-python-deploy

## Notes

Three main steps to deploying chatbots on Facebook messenger:

* Create a server which listens to messages from Facebook - using `flask`
* Define a function for sending messages back to users - using `requests`
* Forward a `https` connection to your local machine - using `ngrok`

### Wrap your chatbot in an http server

* First step - create an https server which listens to messages sent by Facebook
  * Gets a response to that message
  * Sends the response back to the user on Facebook
* `Flask` will be used to create this server



```markdown
1. Create a `flask` app that listens for messages sent to **localhost:5000/webhook**. When messages are sent on Facebook, they will arrive as http requests to this URL

2. The **listen()** function handles these http requests and checks that they contain a valid Facebook message

3. If the message received is valid, the **get_bot_response()** function is called, and the response is sent back to Facebook Messenger
```



## Snippets

#### Server.py

```python
from flask import Flask, request

app = Flask(__name__)

FB_API_URL = 'https://graph.facebook.com/v2.6/me/messages'
VERIFY_TOKEN = ''
PAGE_ACCESS_TOKEN = ''

def get_bot_response(message):
    """Returns a variation of what the user sent"""
    return "This is a dummy response to '{}'".format(message)

def verify_webhook(req):
    if req.args.get("hub.verify_token") == VERIFY_TOKEN:
        return req.args.get('hub.challenge')
   	else:
        return "incorrect"
    
def respond(sender, message):
    """Formulate a response to the user and
    pass it on to a function that sends it."""
    response = get_bot_response(message)
    send_message(sender, response)


def is_user_message(message):
    """Check if the message is a message from the user"""
    return (message.get('message') and
            message['message'].get('text') and
            not message['message'].get("is_echo"))


@app.route("/webhook")
def listen():
    """This is the main function flask uses to 
    listen at the `/webhook` endpoint"""
    if request.method == 'GET':
        return verify_webhook(request)

    if request.method == 'POST':
        payload = request.json
        event = payload['entry'][0]['messaging']
        for x in event:
            if is_user_message(x):
                text = x['message']['text']
                sender_id = x['sender']['id']
                respond(sender_id, text)

        return "ok"    
```



## More Links

https://medium.com/@nidhog/how-to-make-a-chatbot-on-slack-with-python-82015517f19c

https://www.freecodecamp.org/news/how-to-build-a-basic-slackbot-a-beginners-guide-6b40507db5c5/