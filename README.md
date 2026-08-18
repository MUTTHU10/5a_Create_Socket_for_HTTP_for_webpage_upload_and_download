# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
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
~~~
server.py
import socket
import os

HOST = '127.0.0.1'
PORT = 8080

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((HOST, PORT))
server.listen(5)

print(f"Server running on {HOST}:{PORT}")

while True:
    client, address = server.accept()

    request = client.recv(4096)
    request_text = request.decode(errors='ignore')

    print("\nRequest received:")
    print(request_text)

    # Upload
    if request_text.startswith("POST /upload"):
        headers, file_data = request_text.split("\r\n\r\n", 1)

        with open("example.txt", "w") as file:
            file.write(file_data)

        response = "HTTP/1.1 200 OK\r\n\r\nFile uploaded successfully!"

    # Download
    elif request_text.startswith("GET /example.txt"):
        if os.path.exists("example.txt"):
            with open("example.txt", "r") as file:
                file_data = file.read()

            response = (
                "HTTP/1.1 200 OK\r\n"
                "Content-Type: text/plain\r\n"
                f"Content-Length: {len(file_data)}\r\n"
                "\r\n"
                + file_data
            )
        else:
            response = "HTTP/1.1 404 Not Found\r\n\r\nFile not found!"

    else:
        response = "HTTP/1.1 404 Not Found\r\n\r\nInvalid request!"

    client.sendall(response.encode())
    client.close()

http.py
import socket
def send_request(host, port, request):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        s.sendall(request.encode())
        response = s.recv(4096).decode()
    return response

def upload_file(host, port, filename):
    with open(filename, 'rb') as file:
        file_data = file.read()
        content_length = len(file_data)
        request = f"POST /upload HTTP/1.1\r\nHost: {host}\r\nContent-Length: {content_length}\r\n\r\n"
        request += file_data.decode()
        response = send_request(host, port, request)
    return response

def download_file(host, port, filename):
    request = f"GET /{filename} HTTP/1.1\r\nHost: {host}\r\n\r\n"
    response = send_request(host, port, request)
    # Assuming the response contains the file content after the headers
    file_content = response.split('\r\n\r\n', 1)[1]
    with open(filename, 'wb') as file:
        file.write(file_content.encode())

if __name__ == "__main__":
    host = '127.0.0.1'
    port = 8080

    # Upload file
    upload_response = upload_file(host, port, 'example.txt')
    print("Upload response:", upload_response)

    # Download file
    download_file(host, port, 'example.txt')
    print("File downloaded successfully.")
~~~
## OUTPUT

<img width="1356" height="725" alt="image" src="https://github.com/user-attachments/assets/ea8278f8-c51d-4e98-9cc5-5c839b0688f3" />

## Result
Thus the socket for HTTP for web page upload and download created and Executed
