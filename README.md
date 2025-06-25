# To-Do Application Project - Setup & Guide

## Project Structure
This project is a simple To-Do app built using:
- Quasar Framework (Vue.js based UI)
- Axios (for API requests)
- JSONPlaceholder (mock API)
- Typescript

## Prerequisites
Make sure you have the following installed (or updated to its latest version):
- Node.js (recommended: v16+)
- npm

## How to install
1. Unzip project file to folder
2. Open a terminal (powershell or command prompt) and navigate to the project directory:
	cd todo-app-project
2.2. Install the dependencies

## How to run the app
1. After installation is successful, run the server:
	quasar dev
1.1. If not recognized, install Quasar CLI (globally):
	npm install -g @quasar/cli
2. Wait for it to compile and communicate with the server, client, and browser

## How it works
This app loads the tasks from the mock API (JSONPlaceholder).
Here are the actions you can do:
a.) Add new tasks - simply type in your new task into the input field
		- when done, click the "Submit" button to display it
b.) Edit tasks - click on the "Edit" button to edit the details of the task
		- If you are done editing, press the 'Save' button
			- The 'Save' button is also the way out if you want to cancel editing
c.) Delete tasks - click on the delete button to delete a task. There is no confirmation prompt made just yet


## Criterion checklist
1.) Form with input field and submit button
2.) Tasks list displays the data from the mock API (GET)
3.) Add new tasks to display (POST)
4.) Edit tasks and displays the new information (PUT)
5.) Delete tasks from view (DELETE)

## Disclaimers
Notes:
1.) JSONPlaceholder is fake API, therefore, data is not permanently saved to the server
2.) New tasks and edits are kept using the localStorage of the session.
