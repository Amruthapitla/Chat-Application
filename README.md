# Chat-Application
import socket
import threading

def handle_client(client_socket):
    """Handles incoming messages from a client."""
    while True:
        try:
            message = client_socket.recv(1024).decode('utf-8')
            if message:
                print(f"Client: {message}")
            else:
                break
        except:
            print("Connection closed by the client.")
            break

def start_server():
    """Starts the server to listen for client connections."""
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.bind(('127.0.0.1', 5555))
    server.listen(1)
    print("Server is running and waiting for a connection...")

    client_socket, client_address = server.accept()
    print(f"Connected to client at {client_address}")

    threading.Thread(target=handle_client, args=(client_socket,)).start()

    while True:
        message = input("You: ")
        client_socket.send(message.encode('utf-8'))


def start_client():
    """Starts the client to connect to the server and send messages."""
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client.connect(('127.0.0.1', 5555))
    print("Connected to the server. You can start chatting now.")

    def receive_messages():
        while True:
            try:
                message = client.recv(1024).decode('utf-8')
                if message:
                    print(f"Server: {message}")
                else:
                    break
            except:
                print("Connection closed by the server.")
                break

    threading.Thread(target=receive_messages).start()

    while True:
        message = input("You: ")
        client.send(message.encode('utf-8'))

if __name__ == "__main__":
    mode = input("Do you want to start the server or client? (server/client): ").strip().lower()
    if mode == 'server':
        start_server()
    elif mode == 'client':
        start_client()
    else:
        print("Invalid input. Please enter 'server' or 'client'.")
