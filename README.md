# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## NAME: SHIVARAM M.
## Reg.NO.: 212223040195
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program 
### Server.py
```
# Server code

import socket
import os

def start_server(host='127.0.0.1', port=8080):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server_socket:
        server_socket.bind((host, port))
        server_socket.listen(1)
        print(f"Server started at http://{host}:{port}")
        
        while True:
            client_socket, addr = server_socket.accept()
            print(f"Connection from {addr}")
            
            with client_socket:
                request = client_socket.recv(1024).decode()
                headers = request.splitlines()
                
                if headers[0].startswith("POST /upload "):
                    content_length = int([line.split(": ")[1] for line in headers if line.startswith("Content-Length")][0])
                    file_data = client_socket.recv(content_length)
                    with open("uploaded_page.html", "wb") as f:
                        f.write(file_data)
                    response = "HTTP/1.1 200 OK\r\n\r\nFile uploaded successfully!"
                
                elif headers[0].startswith("GET /download "):
                    if os.path.exists("uploaded_page.html"):
                        with open("uploaded_page.html", "rb") as f:
                            file_data = f.read()
                        response = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n" + file_data.decode()
                    else:
                        response = "HTTP/1.1 404 Not Found\r\n\r\nFile not found!"
                
                client_socket.sendall(response.encode())
```
### Client.py
```
# Client code
import socket
import os

def upload_page(file_path, host='127.0.0.1', port=8080):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as client_socket:
        client_socket.connect((host, port))
        
        with open(file_path, "rb") as f:
            file_data = f.read()
        
        request = f"POST /upload HTTP/1.1\r\nHost: {host}:{port}\r\nContent-Length: {len(file_data)}\r\n\r\n".encode() + file_data
        client_socket.sendall(request)
        
        response = client_socket.recv(1024).decode()
        print("Server response:", response)

def download_page(host='127.0.0.1', port=8080):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as client_socket:
        client_socket.connect((host, port))
        
        request = f"GET /download HTTP/1.1\r\nHost: {host}:{port}\r\n\r\n".encode()
        client_socket.sendall(request)
        
        response = client_socket.recv(4096).decode()
        print("Downloaded page content:")
        print(response.split("\r\n\r\n", 1)[1])
```

## OUTPUT
<img width="1680" alt="HTTP_Socket" src="https://github.com/user-attachments/assets/c105de4e-41a9-49a7-a830-bcc02e186abf">


## Result
Thus the socket for HTTP for web page upload and download created and Executed
