# socketio_snippets

### 01
```json
{
  "name": "socketio-chat",
  "version": "0.0.1",
  "description": "mmp major chat app",
  "type": "module",
  "dependencies": {}
}
```

### 02
```bash
npm install express@4
```

### 03
```javascript
import express from 'express';
import { createServer } from 'node:http';

const app = express();
const server = createServer(app);

app.get('/', (req, res) => {
  res.send('<h1>Hello world</h1>');
});

server.listen(3000, () => {
  console.log('server running at http://localhost:3000');
});
```

### 04
```javascript
res.sendFile(new URL('./index.html', import.meta.url).pathname);
```

### 05
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>MMP Major Chat App</title>
    <link rel="stylesheet" href="style.css" type="text/css">
</head>
<body>
<ul id="messages"></ul>
<form id="form" action="">
    <input aria-label="chat message" id="input" autocomplete="off" placeholder="Your message" />
    <button>Send</button>
</form>
<script src="client.js" type="module"></script>
</body>
</html>
```

### 06
```css
body {
    margin: 0;
    padding-bottom: 3rem;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
}

#form {
    background: rgba(0, 0, 0, 0.15);
    padding: 0.25rem;
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    height: 3rem;
    box-sizing: border-box;
    backdrop-filter: blur(10px);
}

#input {
    border: none;
    padding: 0 1rem;
    flex-grow: 1;
    border-radius: 2rem;
    margin: 0.25rem;
}

#input:focus {
    outline: none;
}

#form > button {
    background: #333;
    border: none;
    padding: 0 1rem;
    margin: 0.25rem;
    border-radius: 3px;
    outline: none;
    color: #fff;
}

#messages {
    list-style-type: none;
    margin: 0;
    padding: 0;
}

#messages > li {
    padding: 0.5rem 1rem;
}

#messages > li:nth-child(odd) {
    background: #efefef;
}

#messages > li > span.name {
    font-style: italic;
    font-size: 60%;
    width: 75px;
    display: inline-block;
}
```

### 07
```javascript
app.use(express.static("public"));
```

### 08 
```javascript
import { Server } from 'socket.io';
const io = new Server(server);
io.on('connection', (socket) => {
  console.log('a user connected');
	socket.on('disconnect', () => {
    console.log('user disconnected');
  });
});
```

### 09
```javascript
const form = document.querySelector('#form');
const input = document.querySelector('#input');

form.addEventListener('submit', (e) => {
    e.preventDefault();
    if (input.value) {
      console.log(input.value);
      input.value = '';
    }
});
```

### 10
```javascript
const messages = document.getElementById('messages');
socket.on('broadcast_chat', (msg) => {
  const item = document.createElement('li');
  item.textContent = msg;
  messages.appendChild(item);
  window.scrollTo(0, document.body.scrollHeight);
});
```

### 11
```javascript
const io = new Server(server, {
  connectionStateRecovery: {}
});
```

### 12
```javascript
import sqlite3 from 'sqlite3';
import { open } from 'sqlite';

const db = await open({
    filename: 'chat.db',
    driver: sqlite3.Database
});

await db.exec(`
  CREATE TABLE IF NOT EXISTS messages (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      msg TEXT,
      username TEXT
  );
`);
```

### 13
```javascript
let result;
		try {
		  result = await db.run('INSERT INTO messages (msg, username) VALUES (?, ?)', msg, username);
    } catch (e) {
      console.error('error on inserting message into database', e)
      return;
    }
io.emit('broadcast_chat', msg, username, result.lastID);
```

### 14
```javascript
const socket = io({
    auth: {
        serverOffset: 0
    }
});
```

### 15
```javascript
if (!socket.recovered) {
        try {
            await db.each('SELECT id, msg, username FROM messages WHERE id > ?',
                [socket.handshake.auth.serverOffset || 0],
                (_err, row) => {
                    socket.emit('broadcast_chat', row.msg, row.username, row.id);
                }
            )
        } catch (e) {
            console.error('error on reading old messages from database', e)
        }
    }
```
