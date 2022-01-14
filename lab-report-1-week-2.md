# How To Log into a Course-specific Account on `ieng6`:


### Step 1: Install Visual Studio Code

![Image](cse15l-lab-report-1-step-1)

- On the [Visual Studio Code Website](https://code.visualstudio.com/), press the button to download the program on your computer.

- Make sure it is the correct version for your operating system.

- Follow any instructions that the program might give when installing it.


### Step 2: Remotely Connecting

![Step 2 Image](cse15l-lab-report-1-step-2-2.png)

- If your computer runs on Windows, you will first need to install OpenSSH by following the steps on [this website](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse). You will only need to complete the section titled "Install OpenSSH using Windows Settings".

- Once that is complete or if you have a non-Windows operating system, you will need to look up your CSE 15L account through [this website](https://sdacs.ucsd.edu/~icc/index.php).

- After you have logged in to the website, look for your ETS account starting with "cs15l". It should look something like "cs15lwi22hdf", where the 4 characters after "cs15l" display the quarter that you enrolled in the class, and the last 3 characters are specific to your account.

- Next, you will need to open up a terminal in VS Code by clicking Terminal --> New Terminal in the menu at the top of the screen.

- Enter the command `ssh \<your ETS account>@ieng6.ucsd.edu` , and replace \<your ETS account> with the account number that you found earlier that looks something like "cs15lwi22hdf". You may get a message asking if you are sure you want to connect; type yes and press enter to continue. This happens only when you log in for the first time.

- Enter your password into the terminal, and the output should look something like this: ![Step 2](cse15l-lab-report-1-step-2.png)

- Your terminal is now remotely connected to one of the computers in the CSE basement, meaning you can run commands on that computer just like you would on your own computer.

### Step 3: Trying Some Commands

![Step 3 Image](cse15l-lab-report-1-step-3.png)

- Try running some of the following commands in the terminal, and taking note of what they do:

    ```
    cd ~
    cd <directory>
    ls -lat
    ls -a
    ls <directory>
    ```
- It may also help your understanding if you run them on your own computer's terminal (by ending the remote connection). You can always sign back in using the ssh command from earlier.

- You can end the remote connection by typing `exit` in the terminal or pressing Ctrl + D.

### Step 4: Moving Files With scp

![Step 4 Image](cse15l-lab-report-1-step-4.png)

- The `scp` terminal command is used to copy files between your local computer and the remote connection.

- Assuming you have Java installed, create a new file somewhere on your local computer with the following contents:
    ```
    class WhereAmI {
        public static void main(String[] args) {
            System.out.println(System.getProperty("os.name"));
            System.out.println(System.getProperty("user.name"));
            System.out.println(System.getProperty("user.home"));
            System.out.println(System.getProperty("user.dir"));
        }
    }
    ```
- If you do not have Java installed, you can copy over any file, but it will not have any interesting output in the remote connection.

- In the terminal on your computer, run the following command to copy the file between your computer and the remote connection (remember to add your specific ETS account number):

    `scp WhereAmI.java cs15lwi22@ieng6.ucsd.edu:~/`
- If you then connect to `ieng6` again and use the `ls` command in the terminal, you should see the file listed in the output.

- Run the file using `javac` and `java`. You should see information about the remote computer and its filesystem. If you were to run that file while not connected remotely, you would see information about your own computer instead.

### Step 5: Setting an SSH Key

![Step 5 Image](cse15l-lab-report-1-step-5.png)

- In order to save time by not entering your password every time you log in to ssh, you can create an ssh key that is shared between your computer and the remote connection.

- On your computer enter the following command: `ssh-keygen` . When it prompts for the file in which to save the key, enter the following path (excluding quotation marks), and replacing your name as appropriate: "/Users/Cody/.ssh/id_rsa".

- Then, enter the password you would like to use to log in every time you connect. Another option is to just press enter if you would like to not have to put in a password when you connect.

- There should be some output generating a fingerprint and a randomart image.

- If you are on Windows, you will need to open PowerShell or the Command Prompt and enter `ssh-keygen -t ed25519` in the terminal. It should ask for a file in which to save the key, and you can simply press enter to choose the default path. Then, enter the following commands separately:
    ```
    Get-Service ssh-agent | Set-Service -StartupType Manual

    Start-Service ssh-agent

    Get-Service ssh-agent

    ssh-add ~\.ssh\id_ed25519
    ```

- Next, you will need to connect to `ieng6` again in order to copy the public key to the .ssh directory of your user account on the server:
    ```
    ssh cs15lwi22zz@ieng6.ucsd.edu
    <Enter Password>

    mkdir .ssh
    <logout>

    scp /Users/Cody/.ssh/id_rsa.pub cs15lwi22@ieng6.ucsd.edu:~/.ssh/authorized_keys
    ```

- After this, you should be able to use `ssh` and `scp` without entering your password every time.

### Step 6: Optimizing Remote Running

![Step 6 Image](cse15l-lab-report-1-step-6.png)

- Now that you have set up an ssh key, connecting to `ieng6` is a lot quicker and easier. However, you can make your work even faster using the following tricks.

- If you enter `ssh cs15lwi22@ieng6.ucsd.edu "<command>"`, you can run any command a single time on the remote server, and then immediately exit.

- Additionally, you can separate multiple commands separated by semicolons in one line to run all of them as if they were one command. For example: `ls -a; mkdir folder; cd folder; cd ~` would run each of those commands without you having to enter them separately.

- One way to combine these two skills is with the command `ssh cs15lwi22@ieng6.ucsd.edu "javac WhereAmI.java; java WhereAmI"; javac WhereAmI.java; java WhereAmI` . This will show you the information about the remote connection and then information about your computer, just like you did earlier, but all in one line.
