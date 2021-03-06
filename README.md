# Microservices with a Game On! Room

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/88c9a48c21544cffa2965cfa0def40bb)](https://www.codacy.com/app/gameontext/gameon-room-nodejs?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=gameontext/gameon-room-nodejs&amp;utm_campaign=Badge_Grade)

[Game On!](https://game-on.org/) is both a sample microservices application, and a throwback text adventure brought to you by the wasdev team at IBM. This application demonstrates how microservice architectures work from two points of view:

1. As a Player: navigate through a network/maze of rooms, where each room is a unique implementation of a common API. Each room supports chat, and interaction with items (some of which may be in the room, some of which might be separately defined services as well).
2. As a Developer: learn about microservice architectures and their supporting infrastructure by extending the game with your own services. Write additional rooms or items and see how they interact with the rest of the system.

You can learn more about Game On! at [http://game-on.org/](http://game-on.org/).

##Introduction

This walkthrough will guide you through adding a room to a running Game On! microservices application.  You will be shown how to setup a Node.js room that is deployed as a Cloud Foundry application in Bluemix.

### Installation prerequisites

When deployed using an instant runtime, Gameon-room-nodejs requires:

- [Bluemix account](https://console.ng.bluemix.net)
- [IBM DevOps Services Account](https://hub.jazz.net/register)
- [GitHub account](https://github.com/)

## Create Bluemix accounts and log in
To build a Game On! room in Bluemix, you will first need a Bluemix account.

### Sign up and log in to Bluemix and DevOps
Sign up for Bluemix at https://console.ng.bluemix.net and DevOps Services at https://hub.jazz.net. When you sign up, you'll create an IBM ID, create an alias, and register with Bluemix.

## Get Game On! ID and Shared Secret
For a new room to register with the Game On! application, you must first log into game-on.org and sign in using one of several methods to get your **Game On! ID** and **Shared Secret**.

1.  Go to [https://game-on.org/](https://game-on.org/) and click **Enter**.
2.  Select an authentication method and log in with your user name and password for that type.
3.  View your user profile using the link in the top right.  It is either your username or a person icon.
4.  You should now see your **Game On! ID** and **Shared Secret** near the bottom of the page.

## Registering your room

In production its likely that multiple instances of your service will exist. As a result of this, by default the sample rooms do not register themselves (you need to provide the GAMEON_ID and GAMEON_SECRET to do this). The preferred way to register a room is either via the [command line regutil tool](https://github.com/gameontext/regutil) or via the interactive map. Below are the instructions for using the interactive map to register a new room in GameOn!.

1.  Go to the [interactive map service](https://game-on.org/interactivemap)
2.  Under **Tools**, select **Room Developer**.
3.  Set your Game On! id and Shared Secret fields that you obtained in the steps above, and then close the window.
4.  From the **Tools** menu again, select **Create new room**.
5.  Under the **Room** tab set the **Name**, **Full Name** and **Description**.
6.  Under Connection, set the web socket url that your room will be avilable on. If you don't yet know this you can leave this blank and update it later.

## Deploying as a Cloud Foundry app

### Getting the source code

Our source code is stored on GitHub.

1. Go to the project's [GitHub](https://github.com/gameontext/gameon-room-nodejs) repository and fork it to your own GitHub repository.
2. Navigate to [IBM DevOps](https://hub.jazz.net/).
3. Click **CREATE PROJECT**.
4. Enter a name for your Project.
5. Select **Link to an existing GitHub repository**.
6. Select **Link to a Git Repo on GitHub**.
7. Choose your newly forked project from the dropdown menu that appears.
8. Choose your **Region**, **Organization**, and **Space**.  Generally the defaults will be sufficient.
9. Click **CREATE**.  This will fork your GitHub project into IBM DevOps services, and redirect you to your new project.

### Setup environment variables

Once you have created your new project, you will be able to configure the room to your liking.

1. From your [IBM DevOps](https://hub.jazz.net/) project, click **EDIT CODE** at the upper right corner of your project's overview page.
2. Click on the **Play** button found above the README, this will deploy your app to Bluemix.
3. Click on **DASHBOARD** at the top right of the page to navigate to your [Bluemix](https://bluemix.net) dashboard.
4. Select the newly deployed application which will be named after your project.
5. Click **Environment Variables** on the left side of your application's Overview page.
6. Click on **USER-DEFINED** and then enter each of the following variables:
 - Click **ADD**, for **Name** enter ROOM_NAME, for **Value** enter what you'd like your room to be named.
 - Click **ADD**, for **Name** enter FULL_NAME, for **Value** enter what you'd like your room's full name to be.
 - Click **ADD**, for **Name** enter DESCRIPTION, for **Value** enter what you'd like your room's description to be.
**Please Note:** If you want to register your room directly from here you can do this by setting the following additional environment variables:
 - Click **ADD**, for **Name** enter GAMEON_ID, and use the **Game On! ID** you got earlier for **Value**.
 - Click **ADD**, for **Name** enter GAMEON_SECRET, and use the **Shared Secret** you got earlier for **Value**.
7. Click **SAVE**.

## Deploy to IBM Container service

##### Installation prerequisites

1. [Cloud foundry API](https://github.com/cloudfoundry/cli/releases)
2. [Install the IBM Containers plugin](https://console.ng.bluemix.net/docs/containers/container_cli_cfic_install.html)

##### Deploying as a single container

1. Log in to the IBM container service. This needs to be done in two stages:
  1. Log into the Cloud Foundry CLI using `cf login`. Ypu will need to specify the API endpoint as `api.ng.bluemix.net` for the US South server, or `api.eu-gb.bluemix.net` for the UK server.
  2. After this run the command `cf ic login`. This will perform the authentication to the IBM Container Service.
2. Build the container in the Bluemix registry by running the command  `cf ic build -t gonode .` from the root of the project.
3. Run `cf ic images` and check your image is available.
4. Start the container by running the command `cf ic run -p 3000 --name gonode <registry>/<namespace>/gonode`. You can find the full path from the output of `cf ic images`. An example would be:

  `cf ic run -p 3000 --name gonode registry.ng.bluemix.net/pavittr/gonode`

5. While you are waiting for the container to start, request a public IP address using the command `cf ic ip request`. This will return you a public IP address you can bind to your container.
6. With the returned IP address, bind it using the command `cf ic ip bind <ip address> gonode`.
7. Issue `cf ic ps`, and wait for your container to go from "Networking" to "Running".
8. You should now be able to access your room from GameOn!

#### Deploy as a container group

Instead of deploying a container as a single instance, you can instead deploy a container group. A container group can be used to deploy multiple instances of the same container and load balance between them.

1. Log in to the IBM container service. This needs to be done in two stages:
  1. Log into the Cloud Foundry CLI using `cf login`. Ypu will need to specify the API endpoint as `api.ng.bluemix.net` for the US South server, or `api.eu-gb.bluemix.net` for the UK server.
  2. After this run the command `cf ic login`. This will perform the authentication to the IBM Container Service.
2. Run `cf ic images` and check the `gonode` image is available. If not, run the command `cf ic build -t gonode .` from the root of the project to create it.
3. Create the container group by running `cf ic group create -p 3000 -n <appName> --name gonodegroup <registry>/<namespace>/gonode`. You can find the full path from the output of `cf ic images`. An example would be:

  `cf ic group create -p 3000 --name gonodegroup registry.ng.bluemix.net/pavittr/gonode`

4. Run the command ` cf ic route map -n <appHost> -d mybluemix.net  gonodegroup`. This will make your containers available at <appHost>.mybluemix.net.
5. Run the command `cf ic group instances gonodegroup` to check the status of your instances. Once they are in "Running" state your group is ready to use.
6. You will now be able to access the web socket of your room on `http://<appName>.mybluemix.net`

## Access room on Game On!
Once the room is set up and it has registered with Game On!, it will be accessible on [Game On!](https://game-on.org/). It may take a moment for the room to appear.

1. Log in to [Game On!](https://game-on.org/) using the authentication method you used to create your user ID and shared secret for the registered room.
2. Use the Game On! command `/listmyrooms` from The First Room, to see your list of rooms. Once your room is registered, it will appear in that list.
3. To get to your room, navigate through the network or go directly there by using the `/teleport` command from The First Room.
4. Look at the Bluemix log console to see "A new connection has been made to the room" command from The First Room.

### List of host provided commands
The Game On! host provides a set a universal commands:
- **/exits** - List of all exits from the current room.
- **/help** - List all available commands for the current room.
- **/sos** - Go back to The First Room.

### The First Room commands
The First Room is usually where new users will start in Game On!. From there, additional commands are available and maintained by Game On!. For the list of current commands use the `/help` command.
