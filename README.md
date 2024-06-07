# deploy-flask-on-linux
How to setup and deploy the flask project on linux in production

# Installation:
## 1 Install Python Version Management Tool:
-	Python version managment tool is used to install specific version of python for different project according to the requiremnts.
-	One commonly used Python version management tool is pyenv. You can install pyenv on Linux using its installer script. Open termial and paste:
  ```sh
    curl https://pyenv.run | bash
 ```
## 2 Add pyenv to Your Shell:
- After installing pyenv, you need to add it to your shell's configuration file (e.g., .bashrc, .bash_profile, .zshrc, etc.) to make it available in your terminal sessions.
- Check the .bash_profile exist:
  ```
    ls ~/.bash_profile
  ```
  if the file does not exist it returns the error "No such file or directory"
- Open file:
  ```
    nano ~/.bash_profile
  ```

- add the following lines
  ```
  export PYENV_ROOT="$HOME/.pyenv"
  [[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
  eval "$(pyenv init -)"
  ```

## 3 Install Python:
- With pyenv installed, you can install specific Python versions for your projects. For example, to install Python 3.9.7, use:
  ```
    pyenv install 3.9.7
  ```
  You can install multiple Python versions side by side using pyenv according to the projects requirement.
  - List all installed Python versions managed by pyenv:
    ```
    pyenv versions
    ```

## 4 Cloning Repository and Dependency Installation

- Clone your Git repository or pull the latest changes if you've already cloned it:
    ```bash
    git clone <repository_url>
    ```
## 5 Set Python version & Create virtual enviroment:
- Navigate to the project root directory using termianl
- Set the local Python version for your project to your required python version installed using pyenv, for our case 3.9.7
```
  pyenv local 3.9.7
  python --version
```
  You can set different local versions for different projects by running this command in the root folder of that project.

- Create virtual enviroment using the venv a built tool in python
```
python -m venv project_env
```

- Activate Virtual enviroment
```source prject_env/bin/activate ```


## 6 Install Dependencies:

-  Install project-specific dependencies using pip and a requirements.txt file
-  Make sure you have requirements.txt file in you project root folder.
``` pip install -r requirements.txt ```


## 7 Install & Setup Gunicorn:
- Gunicorn is used as the WSGI HTTP server to serve your Flask application. It's responsible for handling incoming HTTP requests, managing worker processes, and routing requests to your Flask application.
- Instll gunicorn in current virtual enviroment	
``` pip install gunicorn ```
- Create a file in the project root directory gunicorn.conf.py
- Add the following lines:
``` bind '0.0.0.0:8000'  # or any port on which you wanted flask to listed
  workers = 3  		# Adjust the worker according to the cpu cores and your system
  loglevel 		# 'info'
  max_requests = 1000
  worker_connections = 1000
```
### Loglevel:
*Info is a moderate level of logging that includes informational messages about the application's status, requests, and responses. It's often used for general operational logs to track the application's behavior.
other loglevel options are:*
	- debug
	- warning
	- error
	- critical
 ### max_requests: 
		
- *The max_requests setting in Gunicorn's configuration specifies the maximum number of requests a worker process can handle before it is recycled (i.e., restarted). When a worker process reaches the specified number of requests, Gunicorn will gracefully restart that worker process to prevent potential memory leaks or other issues that may accumulate over time.*
		
- *The purpose of setting max_requests is to prevent long-running worker processes from accumulating memory leaks or other resource issues. By periodically restarting worker processes, Gunicorn ensures that they start with a clean state, which can help maintain stability and performance.*
	

### worker_connections:
- *The purpose of setting worker_connections is to control the concurrency level of each worker process. It helps manage the number of simultaneous connections that can be served by a single worker, preventing overload and improving performance by distributing the workload effectively*

## 8 Install Supervisor:
- Supervisor is used to manage and monitor the Gunicorn process that runs your Flask application. It ensures that Gunicorn stays running, restarts it if it crashes, and provides logging and monitoring capabilities.
- Install supervisor by writing the following command in the terminal:
  ```sudo apt-get install supervisor```

## 9 Configure Supervisor:
- Write the following command in the terminal
  ```cd /etc/supervisor/conf.d/
	  sudo touch project_name.conf
	  sudo nano project_name.conf.conf
  ```
- As the file open  write the following lines in the gunicorn.conf file
  ``` [program:project_name]
	  Directory=/root/path/to/project/
	  command=/root/path/to/project/venv/bin/gunicorn -c /root/path/to/project/gunicorn.conf.py initializer.wsgi:app
	  autostart=true
	  autorestart=true
	  stderr_logfile=/var/log/gunicorn/gunicorn.err.log
	  stdout_logfile=/var/log/gunicorn/gunicorn.out.log
  ```

	*Note: binding address in gunicorn.conf.py would be same as written in Nginx config file that you will configure after this other wise 502 error will come.*

## 10 Restart Supervisor:
- Create the log files directory if not created
```sudo mkdir /var/log/gunicorn }
{ sudo systemctl restart supervisor
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl status
```

-- Now your flask project is started in process created by supervisor check that status of currently runnning process
``` sudo supervisorctl status ```
	
-- if there is some sytaxt error you can check from this command:
```sudo supervisord -n -c /etc/supervisor/supervisord.conf ```
