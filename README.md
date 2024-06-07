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

## Cloning Repository and Dependency Installation

- Clone your Git repository or pull the latest changes if you've already cloned it:
    ```bash
    git clone <repository_url>
    ```
