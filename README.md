Project Structure Blueprint
flask-hello-microservice/
│
├── app.py                    # Main Flask application file
├── requirements.txt          # Python dependencies list
└── Dockerfile               # Docker image build instructions
Structure Explanation:

app.py: Contains the Flask application logic and routes
requirements.txt: Lists all Python packages needed (Flask)
Dockerfile: Instructions for Docker to build the container image
File Count: 3 files Directories: 0 subdirectories Purpose: Minimal microservice setup for serving a simple message

Objective
Create a simple microservice application using Docker and Python Flask that serves a "Hello, World!" message. This experiment introduces the fundamental concepts of containerization and microservice architecture.

Prerequisites
Docker installed on your system
Basic understanding of Python
Text editor or IDE
Step 1: Create a Directory for the Application
mkdir flask-hello-microservice
cd flask-hello-microservice
Explanation:

mkdir flask-hello-microservice: Creates a new directory to organize all project files
cd flask-hello-microservice: Changes the current working directory to the newly created folder
Why this matters: Organizing files in a dedicated directory follows best practices for project management and makes deployment easier
Step 2: Create a Python File (app.py)
touch app.py
Explanation:

Creates an empty Python file that will contain our Flask application code
app.py is the conventional name for the main application file in Flask projects
Step 3: Add the Following Code to 'app.py'
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, World!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
Detailed Line-by-Line Explanation:

Line 1: from flask import Flask

Imports the Flask class from the flask module
Flask is a micro web framework for Python that allows us to build web applications
The Flask class is used to create our application instance
Line 3: app = Flask(__name__)

Creates an instance of the Flask application
__name__ is a special Python variable that represents the name of the current module
Flask uses this to determine the root path of the application, which helps locate resources like templates and static files
The app object will handle HTTP requests and responses
Line 5: @app.route('/')

This is a Python decorator that binds a URL route to a function
The '/' represents the root URL of the application (e.g., http://localhost:5000/)
When a user visits this URL, Flask will execute the function immediately below this decorator
Routes are the mechanism by which Flask maps URLs to Python functions
Line 6-7: def hello():

Defines a view function named hello
This function will be called when someone accesses the root URL
View functions are responsible for generating responses to HTTP requests
Line 7: return "Hello, World!"

Returns a simple string response to the client's browser
This text will be displayed when the user visits the application
Flask automatically converts this string into an HTTP response with status code 200 (OK)
Line 9: if __name__ == '__main__':

This conditional checks if the script is being run directly (not imported as a module)
__name__ equals '__main__' only when the file is executed directly
This is a Python idiom that prevents code from running when the module is imported
Line 10: app.run(host='0.0.0.0', port=5000)

Starts the Flask development server
host='0.0.0.0': Makes the server accessible from any network interface
0.0.0.0 means "all IPv4 addresses on the local machine"
This is crucial for Docker containers to accept external connections
Without this, the server would only be accessible from within the container itself
port=5000: Specifies that the server should listen on port 5000
Port 5000 is the default port for Flask applications
This port must match the port we expose in our Dockerfile
Step 4: Create a Requirements File
touch requirements.txt
Explanation:

Creates a file that lists all Python dependencies needed for the project
requirements.txt is the standard convention in Python projects for dependency management
This file enables reproducible environments across different systems
Step 5: Add the Following Content to 'requirements.txt'
Flask==2.3.0
Explanation:

Specifies that our application requires Flask version 2.3.0
The == syntax pins the exact version, ensuring consistency across deployments
Why version pinning matters: Different Flask versions may have breaking changes; pinning ensures your code works reliably
When Docker builds the image, it will install this specific version of Flask
Step 6: Create a Dockerfile
touch Dockerfile
Explanation:

Creates a Dockerfile, which is a text document containing instructions to build a Docker image
Docker images are templates used to create containers
The Dockerfile has no file extension by convention
Step 7: Add the Following Content to the "Dockerfile"
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
Detailed Line-by-Line Explanation:

Line 1: FROM python:3.9-slim

Specifies the base image for our Docker container
python:3.9-slim is an official Python image with Python 3.9 pre-installed
slim variant is a smaller image that includes only essential packages, reducing the final image size
Why this matters: Starting from a base image saves time and ensures a consistent environment
This layer contains the Python interpreter and basic system libraries
Line 3: WORKDIR /app

Sets the working directory inside the container to /app
All subsequent commands will be executed relative to this directory
If the directory doesn't exist, Docker creates it automatically
Best practice: Using a dedicated working directory keeps the container filesystem organized
Line 5: COPY requirements.txt requirements.txt

Copies the requirements.txt file from your local machine (source) to the container (destination)
Syntax: COPY <source> <destination>
The destination path is relative to the WORKDIR (/app/requirements.txt)
Why copy this first: Docker caches layers; by copying requirements separately, we can leverage caching if dependencies don't change
Line 6: RUN pip install -r requirements.txt

Executes a command during the image build process
pip install -r requirements.txt reads the requirements file and installs all listed packages
This creates a new layer in the Docker image with all Python dependencies installed
Important: This happens at build time, not runtime
Line 8: COPY . .

Copies all remaining files from the current directory (.) on your host to the working directory (.) in the container
This includes app.py and any other project files
Placed after installing dependencies to optimize Docker's layer caching
Line 10: EXPOSE 5000

Documents that the container listens on port 5000 at runtime
This is primarily documentation; it doesn't actually publish the port
Other developers and tools can see which ports the application uses
Note: You still need to use -p flag when running the container to actually map ports
Line 12: CMD ["python", "app.py"]

Specifies the default command to run when the container starts
Uses JSON array syntax (exec form), which is preferred over shell form
Executes python app.py, starting our Flask application
Important: Only the last CMD instruction in a Dockerfile takes effect
This runs at container runtime, not during the build
Step 8: Build the Docker Container
docker build -t flask-hello-microservice .
Explanation:

docker build: Command to build a Docker image from a Dockerfile
-t flask-hello-microservice: Tags (names) the image as "flask-hello-microservice"
The -t flag stands for "tag"
This name will be used to reference the image later
.: Specifies the build context (current directory)
Docker will look for a Dockerfile in this directory
All files in this directory are available to COPY commands
What happens during the build:

Docker reads the Dockerfile
Downloads the Python base image (if not cached)
Creates layers for each instruction
Installs Flask and dependencies
Copies application files
Creates a final image tagged with the specified name
Step 9: Run the Docker Container
docker run -d -p 5000:5000 flask-hello-microservice
Explanation:

docker run: Command to create and start a container from an image
-d: Runs the container in detached mode (background)
The container runs independently of your terminal session
Returns the container ID and gives you back the command prompt
-p 5000:5000: Maps port 5000 on the host to port 5000 in the container
Syntax: -p <host_port>:<container_port>
Allows external access to the containerized application
The first 5000 is your local machine's port
The second 5000 is the container's internal port
flask-hello-microservice: Name of the image to run
Access the application:

Open a web browser and navigate to: http://localhost:5000
You should see: "Hello, World!"
Step 10: Stop and Remove the Container
List running containers:

docker ps
Shows all running containers
Note the CONTAINER ID or NAME from the output
Stop the container:

docker stop <container_id>
Gracefully stops the running container
Replace <container_id> with the actual ID from docker ps
The container is stopped but not deleted
Remove the container:

docker rm <container_id>
Permanently removes the stopped container
Frees up system resources
The image remains available for creating new containers
Alternative (force remove running container):

docker rm -f <container_id>
Combines stop and remove in one command
The -f flag forces removal even if the container is running
