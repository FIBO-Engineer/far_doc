# VSCode Setup, SSH Key, and GitHub Basics for ROS Development

## VSCode
Visual Studio Code (VSCode) is a lightweight but powerful source code editor that supports multiple programming languages and extensions.

### Installation
1. Download VSCode from the [official website](https://code.visualstudio.com/).
2. Install it by following the on-screen instructions for your operating system.

### Essential Extensions for ROS Development
To enhance your ROS development experience, consider installing the following extensions:
- **ROS** (Provides tools for working with ROS packages)
- **CMake Tools** (For building ROS packages)
- **Python** (Support for Python development in ROS)
- **C/C++** (Essential for C++ development in ROS)
- **Remote - SSH** (For working with remote ROS robots)
- **GitHub Copilot** (AI-powered coding assistant)

### Basic Usage
- **Open a ROS workspace**: `File > Open Folder`
- **Integrated Terminal**: Open with `Ctrl + \,`
- **Command Palette**: Access with `Ctrl + Shift + P`
- **Version Control**: View changes using the Source Control tab (`Ctrl + Shift + G`)

## SSH Key
SSH keys allow secure authentication with remote servers and Git repositories without using passwords.

### Generating an SSH Key
1. Open a terminal and run:
   ```sh
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   or just
   ```
   ssh-keygen
   ```
2. Press Enter to accept the default location (`~/.ssh/id_ed25519`).
3. Set a secure passphrase (optional but recommended).

### Adding SSH Key to SSH Agent
Start the SSH agent and add your key (should already by default):
```sh
ssh-agent bash
ssh-add ~/.ssh/id_ed25519
```

### Putting Public key into a robot for SSH without Password
```sh
cat ~/.ssh/id_ed25519.pub
```
Then copy this public key and paste to `~/.ssh/authorized_keys`

### Adding SSH Key to GitHub
1. Copy your public key:
   ```sh
   cat ~/.ssh/id_ed25519.pub
   ```
2. Go to [GitHub SSH Keys Settings](https://github.com/settings/keys).
3. Click **New SSH Key**, paste your key, and save it.
4. Test the connection:
   ```sh
   ssh -T git@github.com
   ```
   You should see a success message.

## GitHub Basics
GitHub is a platform for version control and collaboration using Git.

### Configuring Git
Set up your user information:
```sh
git config user.name "Your Name"
git config user.email "your_email@example.com"
```

### Cloning a ROS Repository
To clone a ROS package repository using SSH:
```sh
git clone git@github.com:your-username/ros-package.git
```

### Basic Git Commands
- **Check Status**:
  ```sh
  git status
  ```
- **Add Files to Staging**:
  ```sh
  git add .
  ```
- **Commit Changes**:
  ```sh
  git commit -m "Your commit message"
  ```
- **Push Changes**:
  ```sh
  git push origin main
  ```
- **Pull Latest Changes**:
  ```sh
  git pull origin main
  ```
- **Stash** (Store change to local only, not in timeline):
  ```sh
  git stash
  ```
  or
  ```sh
  git stash save "Your message here"
  ```
- **List Stashes**
  ```sh
  git stash list
  ```
- **Pop Stash** (Apply and remove stash)
  ```sh
  git stash pop stash@{N} # Replace N with stash index
  ```
- **Rebase** (Stash local change, pull, apply local change):
  ```sh
  git pull --rebase
  ```


## ROS-Specific Git Practices
- Use **branching** for feature development (`git checkout -b feature-branch`).
- Follow ROS package **naming conventions**.
- **Tag releases** for stable versions (`git tag -a v1.0 -m "Release version 1.0"`).
- Utilize GitHub **issues** and **pull requests** for collaborative ROS development.

This guide covers the basics to get started with VSCode, SSH keys, and GitHub for ROS development. Happy coding!