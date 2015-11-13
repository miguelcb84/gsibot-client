![GSI Logo](http://gsi.dit.upm.es/templates/jgsi/images/logo.png)
[GSIBot thin client](http://gsi.dit.upm.es) 
==================================

Introduction
---------------------
This is a skeleton implementation of a javascript client used to access bots offering an API as described below.

This code is not final, neither are the API guidelines. Thus, additional parameters may be included either in the request or in the response. However, we strongly advise against changing the name of the current parameters to keep wide client/server compatibility.

### Installation instructions

Since the code corresponds to a jacascript thin client, it needs no installation at all. By cloning the project and accessing to the popup.html example html page, you can use it.

The dummy server used to support this demo may be down. In that case by changing the `action` attribute of the `form` in the `popup.html` you may point to your own server.

    <form id="userinput" method="get" action="<server_url>" >

For more information, contact us through: http://gsi.dit.upm.es

### Demo

For a running demo based on this client visit http://demos.gsi.dit.upm.es/gsibot/ask-client/ (this demo does not follow the API recomendation; however its html and look-an-feel is similar to the ones in this repository)

Bot service API 
-----------------------------

This document aims to provide a common interface for chatterbots. If all bot servers would implement this simple API, any client (web, mobile, chrome-extensions, voice-based, ...) may be connected to any bot service.

### Request format

The request consists on a http get request with the following parameters:

* **q**: The text string the user introduces. Escape it if needed.
* **bot**: The name or id of the bot. Some servers provide different bots, this is used to identify a particular one. It may be skiped, however it may be used to show the name of the bot in the html.
* **user**: Identifies the user. This is used by the server to keep track of conversation with different users. In that regard, other methods may be used such as using the ip or the session if provided.
* **userAgent**: Identify the client. Some clients may offer additional functionalities (like showing pictures, url navigation, ...) this is the first approach to support these. Then, the serve may adapt the respose to the features supported by each useragent. Nonetheless, it can also be used for tracking useragent usage in the server side.
* **type**: The response format this client is expecting to receive. By default it uses `json`, and it is the preferred format.

An example of request would be:

    http://gsi.dit.upm.es/gsibot/TalkToBot?q="Hi there"&bot="Nico"&type="json"&user="miguel"&userAgent="web_html_v1"

In the file `popup.html``provided the form includes most of these parameters. The form that produces the same request shown is as follows (where the value of the 'q' field is "hi there"):

    <form id="userinput" method="get" action="http://gsi.dit.upm.es/gsibot/TalkToBot" >
      <input type="hidden" name="userAgent" value="web_html_v1" />
      <input type="hidden" name="user" value="miguel" />
      <input type="hidden" name="bot" value="Nico" />
      <input type="hidden" name="type" value="json" />
      <input type="text" name="q"/>
      <button type="submit" name="submit" value="send" >Send</button>
    </form>

### Response format

Once a request as described before is received at the server, it produces a response with the structure showed below. The default format is json. Other formats such as xml may be used, althought it is discouraged.

The response consist on a json object `dialog` with the following fields:

* **response**: The reponse of the bot. Usually a text string, although it may include html tags if the client supports it.
* **q**: The userinput sent by the client. Usually this is just an echo of the request param.
* **user**: The name of the user. Usually this is just an echo of the request param.
* **bot**: The name of the bot. This is used to show the name of the bot in the user client. Thus, even if the server only supports a single bot, it should provide a bot name in the response.
* **mood**: String that identifies the mood of the bot. It is used to change the bot's avatar in the client. This is designed this way to allow each client to show mood their own way. An alternative implementation is to provide an avatar's url when the esrver wants the client to change it. However, this may be bound to particular clients that are designed explicitly to access that server.
* **url**: This is an optional parameter that provides a related url in case the response makes reference to a document. It may be a webpage, an image, a document, etc.
* **askFeedback**: A boolean field used to instruct the user to show a feedback form about the last interaction. This was included to avoid massive feedback enqueries e.g. after a "hi" "hello" interaction there is no need to gather feedback. Default is false.

A response may also contain a set of dialog fields. Each `dialog` object represents a utterance (which will end-up being different bubbles in the client interface)

An example of response in json format would be:

    {'dialog':{
        'response':'Hi, how are you today?',
        'q':'hi there',
        'user':'miguel',
        'bot':'Nico',
        'mood':'greetings',
        'url': 'https://upload.wikimedia.org/wikipedia/en/6/65/Hello_logo_sm.gif',
        'askFeedback': false
      }
    }

#### Disclaimer

Al http headers related to content type or COORS handling are not covered in the document, yet they may be required.
