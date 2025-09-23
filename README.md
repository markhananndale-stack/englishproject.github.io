<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modern Messaging</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }
        body {
            background: #f0f2f5;
        }
        .chat-container {
            max-width: 1000px;
            margin: 20px auto;
            background: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            height: 90vh;
            display: flex;
            flex-direction: column;
        }
        .chat-header {
            padding: 20px;
            border-bottom: 1px solid #eee;
            background: #ffffff;
            border-radius: 10px 10px 0 0;
        }
        .chat-messages {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
        }
        .message {
            margin: 10px 0;
            padding: 10px 15px;
            border-radius: 15px;
            max-width: 70%;
            word-wrap: break-word;
        }
        .received {
            background: #f0f2f5;
            float: left;
        }
        .sent {
            background: #0084ff;
            color: white;
            float: right;
        }
        .chat-input {
            padding: 20px;
            border-top: 1px solid #eee;
            display: flex;
            gap: 10px;
        }
        input[type="text"] {
            flex: 1;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 20px;
            outline: none;
        }
        button {
            padding: 10px 20px;
            background: #0084ff;
            color: white;
            border: none;
            border-radius: 20px;
            cursor: pointer;
        }
        button:hover {
            background: #006acc;
        }
    </style>
</head>
<body>
    <div class="chat-container">
        <div class="chat-header">
            <h2>Messages</h2>
        </div>
        <div class="chat-messages" id="messageArea">
            <div class="message received">Hello! How are you?</div>
            <div class="message sent">I'm doing great, thanks!</div>
        </div>
        <div class="chat-input">
            <input type="text" id="messageInput" placeholder="Type a message...">
            <button onclick="sendMessage()">Send</button>
        </div>
    </div>

    <script>
        function sendMessage() {
            const input = document.getElementById('messageInput');
            const messageArea = document.getElementById('messageArea');
            
            if (input.value.trim() !== '') {
                const messageDiv = document.createElement('div');
                messageDiv.className = 'message sent';
                messageDiv.textContent = input.value;
                messageArea.appendChild(messageDiv);
                input.value = '';
                messageArea.scrollTop = messageArea.scrollHeight;
            }
        }

        document.getElementById('messageInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });
    </script>
</body>
</html></div>
