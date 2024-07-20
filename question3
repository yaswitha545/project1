import socket
import time
import json

def get_user_input():
    start_point = input("Enter starting point: ")
    destination = input("Enter destination: ")
    return start_point, destination

def send_api_request(start_point, destination, api_key):
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(("maps.googleapis.com", 443))
    request = f"GET /maps/api/directions/json?origin={start_point}&destination={destination}&key={api_key}&traffic_model=best_guess HTTP/1.1\r\nHost: maps.googleapis.com\r\n\r\n"
    sock.sendall(request.encode())
    response = b""
    while True:
        data = sock.recv(1024)
        if not data:
            break
        response += data
    sock.close()
    
    # Split the response into headers and body
    response_parts = response.decode().split("\r\n\r\n", 1)
    
    # Check the response status code
    status_code = int(response_parts[0].split("\r\n")[0].split(" ")[1])
    if status_code == 200:
        return json.loads(response_parts[1])
    else:
        raise Exception(f"API request failed with status code {status_code}")

def display_traffic_data(traffic_data):
    if "routes" in traffic_data:
        for route in traffic_data["routes"]:
            for leg in route["legs"]:
                print(f"Route: {leg['start_address']} to {leg['end_address']}")
                print(f"Estimated Travel Time: {leg['duration']['text']}")
                for step in leg["steps"]:
                    print(f"Step: {step['html_instructions']}")
                    if "traffic_speed_entry" in step:
                        print(f"Traffic Speed: {step['traffic_speed_entry']['speed']} km/h")
                        if step['traffic_speed_entry']['congestion'] == True:
                            print("Congestion Detected")
                    print()
    else:
        print("Error: Unable to fetch traffic data.")

def main():
    api_key = "YOUR_API_KEY"

    while True:
        start_point, destination = get_user_input()
        try:
            traffic_data = send_api_request(start_point, destination, api_key)
            display_traffic_data(traffic_data)
        except Exception as e:
            print(f"Error: {e}")
        print(f"Last updated: {time.strftime('%Y-%m-%d %H:%M:%S')}")
        print()
        input("Press Enter to continue...")

if __name__ == "__main__":
    main()
