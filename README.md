# sso-integration-demo
A simple page to demonstrate the SSO integration using simple JS for a website.

## Basic SSO usage

Connect to the SSO service, via a websocket client:

    window.socket = new WebSocket("wss://sso.nexusmods.com");

Once connected, retrieve the request ID and connection_token used on the previous connection.

    // retreive previous uuid and token
    uuid = sessionStorage.getItem("uuid");
    token = sessionStorage.getItem("connection_token");
    
Or, if this is the first connection, just generate a random uuid, a token is not needed for first connection

    uuid = uuidv4();
    token = null;
    sessionStorage.setItem('uuid', uuid);
    var data = {
        id: uuid,
        token: token,
        protocol: 2
    };

    // Send the request
    socket.send(JSON.stringify(data));
    
This has informed the SSO server that we are ready to received the user's approval. Once the request is sent, the
SSO will send a message back with the connection_token mentioned above - this is only used on
subsequent connections (usually due to disconnects and network errors). The response from the SSO will
look something like this:

`connection_token:yNiByflxsnptCuIXpMPMkQ60pedN6msN`

The browser can also be opened and the authorise page can be shown.

    // Open the browser window, using the uuid and your application's reference
    window.open("https://www.nexusmods.com/sso?id="+uuid+"&application_id="+application_slug);
    
>Note: An application reference can only be generated for an application by the Nexus Mods staff, so contact the community 
team for more information.

The user will be shown an 'authorise' page, or the login page if they are not logged in. Once they have authorised the token, 
the API key will be sent through the websocket in the following format

`api_key:VX2PMnc4T5Q3dUFmjE45WyRyZ3VkKzIvYVJiMUdick5XQU9QWHdUbFo4...`

This API key can be stored by the client and sent as a header for every HTTP request for the API.