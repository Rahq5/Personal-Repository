
GitHub SSH Key Setup for Ubuntu.md

This guide explains how to connect your local Ubuntu machine to GitHub using SSH.

---

## 1. Check for Existing Keys

First, check if your machine already has an SSH key pair.

* Open your terminal (`Ctrl + Alt + T`).


* Navigate to the `.ssh` directory: `cd ~/.ssh`.


* List the files: `ls`.


* If you see `id_rsa` and `id_rsa.pub`, you already have a key and can skip to the **Copy Public Key** section.



---

## 2. Generate a New SSH Key

If you do not have a key, generate one using this command:

* 
**-t rsa**: Specifies the kind of key to generate.


* 
**-b 4096**: Sets the bit length for security.


* 
**-C**: Adds your email as a comment (metadata).



### Steps during generation:

1. 
**File Location**: Press **Enter** to save the key in the default location (`~/.ssh/id_rsa`).


2. 
**Passphrase**: You will be prompted for a passphrase.


* This is optional but recommended for extra security.


* To use no passphrase, leave it blank and press **Enter**.


* 
**Note**: No characters will appear on the screen while you type the passphrase.





---

## 3. Add Key to GitHub

Once created, you must link the public key to your GitHub account.

1. 
**Display the Public Key**: Run `cat ~/.ssh/id_rsa.pub` and copy the entire output.


2. 
**GitHub Settings**: Log in to GitHub, click your profile picture, and select **Settings**.


3. 
**SSH and GPG Keys**: Select this option from the left sidebar.


4. 
**New SSH Key**: Click the **New SSH key** button.


5. 
**Save**: Enter a **Title** (e.g., "Ubuntu Laptop"), paste your key into the **Key** box, and click **Add SSH key**.



---

## 4. Configure Local Git Settings

Set your global Git identity. This information is attached to your commits.

* 
**Set Username**: `git config --global user.name "Your Name"`.


* 
**Set Email**: `git config --global user.email "your_email@example.com"`.



> [!NOTE]
> Using `--global` applies these settings to every repository on your computer. Removing it applies settings only to the current repository.
> 
> 

---

## 5. Force Git to use SSH

To ensure Git always uses SSH instead of HTTPS for GitHub, run:

---

## 6. Verify and Use

Test your connection:

* Run: `ssh -T git@github.com`.


* If successful, you will see: *"Hi username! You’ve successfully authenticated..."*.



**To clone a repository:**

1. Go to the repository on GitHub and click the **Code** button.


2. Copy the **SSH URL**.


3. In your terminal, run: `git clone <SSH_URL>`.


