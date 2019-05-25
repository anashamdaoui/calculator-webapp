## Directory content
calculator
.--- scr/main/webapp/
.    .--- func               the functional source code of the calculator
.    .--- login              the login page source code
.    .--- WEB-INF/web.xml
.    .--- CalcWeb.jsp
.    .--- Login.jsp
.
.--- pom.xml
.
.--- Dockerfile
.
.--- bin                     the script to build the code and the docker image


## Build

The build script uses mvn package`to produce the .war file and then bundles it with a Docker image that runs Tomcat. 
Usage:

    bin/./build

## What happened

* mvn package was ran and the file`target/calculator.war`built and moved into `pkg/calculator.war`
* a docker image was built and copied the `pkg/calculator.war`inside the docker image at /usr/local/tomcat/webapps/calculator.war.
Check out the [Dockerfile](Dockerfile) for details.

Here's an example of some things to check after running the build script:

    $ ls pkg/
    calculator.war

    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    calculator-webapp   latest              88092dfb7325        6 minutes ago       591MB
    tomcat              8.5                 a92c139758db        2 weeks ago         558MB
    $

## Source Url Mapping

The app is a small calculator java servlet app. Here's the source code to url mapping:

Source | Url
--- | ---
src/main/webapp/login/LoginServlet.java | localhost:8080/calculator
src/main/webapp/Login.jsp | localhost:8080/calculator

## Run

Here are the summarized commands to deploy the web application on a Tomcat server

    $ docker run --rm -p 8080:8080 -d calculator-webapp
      Executes the [Dockerfile](Dockerfile) to start a docker container that runs tomcat and binds the host port 8080 to docker container port 8080 so that you can access the webserver from your browser.
    
    $ docker container ls
      CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
      486eed025bb4        calculator-webapp   "catalina.sh run"   2 hours ago         Up 2 hours          0.0.0.0:8080->8080/tcp   sharp_raman
      Shows the result of docker run command.

    $ docker exec -ti $(docker ps -ql) bash
      Opens a bash shell that runs inside the docker image you just run.

    $ ls /usr/local/tomcat/webapps/
      Check that the latest .war file built is well deployed in tomcat server.
    
    $ curl localhost:8080/calculator
      Tests that the server is responding and the application is well deployed.
      You should see the html code of the login page displayed.

    $ exit
      Exits the bash shell started previously using docker exec command.

    $ docker stop $(docker ps -ql)
      To stop the server.

Then you can hit the the [HOSTNAME]:8080/calculator and to verify that Tomcat is servering the calculator.war file, you should see the html page for the login.

## Dependencies

* docker: `see docker documentation for install.`
* maven: `sudo apt-get install maven on ubuntu.
