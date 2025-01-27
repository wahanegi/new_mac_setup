
## Welcome to the Cloverpop App

This is from the README page from Cloverpop's repository, to help our interns see how we set up new machines.

The Cloverpop application is built on: 
* Ruby 3.2.4
* Rails 7.0
* postgresql 13.9 or higher
* Shakapacker
* Yarn
* Hosted by heroku with asset storage on AWS S3

---
## New Development Machine Install (Mac)

*NOTE:* After installing something new, if something doesn't work like you expect, try quitting and restarting terminal.
1. Install the latest version of XCode from the App store, run `$ xcode-select --install`
2. Install the latest version of Homebrew: http://brew.sh
3. Install Git on Mac using homebrew: `$ brew install git`
4. Set your GIT username from terminal: `$ git config --global user.name "YOUR NAME"`
5. Set your GIT email address from terminal: `$ git config --global user.email "YOUR EMAIL ADDRESS"`
6. Generate and add SSH keys your Github account by following the instructions at https://help.github.com/articles/generating-ssh-keys/
7. Install GPG using homebrew: `$ brew install gpg` (May be needed for RVM in next step)
8. Install the latest version of RVM: https://rvm.io, but instead of `gpg2` Use `gpg` in the command that adds the GPG keys.  If this doesn't work check out the [security](http://rvm.io/rvm/security) page for a workaround.
9. Install Ruby from terminal using RVM: `$ rvm install 3.2.4` (for AppleSilicon add ` --with-openssl-dir=$(brew --prefix)/opt/openssl@3`)
10. Install posgtres from terminal: `$ brew install postgresql@15` and follow its instructions to start the service using `brew services...` command + anything else it suggests.
11. Create postgresql superuser postgres: `$ createuser postgres -s`
12. Change your directory to where you want your work projects in terminal and clone the git repo: `$ git clone git@github.com:my_github_account/my_repo.git`
13. Go into the directory `$ cd my_repo`. Confirm that when you run `$ rvm gemset list` it lists "my_repo" as your gemset.
14. Run `$ gem install bundler`
15. Run `$ gem update --system`
16. Run bundler: `$ bundle`
17. Create a new database: `$ rails db:create`
18. Should be ready to roll: `$ rails s`

### Highly Recommended Mac Goodies
1. For pretty and customizable command line info, including branch and whether you have uncommited changes or not: 
   
        $ brew install zsh-git-prompt

    Then update `~/.zshrc` with the following code:
   
        source "/usr/local/opt/zsh-git-prompt/zshrc.sh"

        PROMPT='%~%b$(git_super_status)\$ '
        
        clean_branches() {
            git remote prune origin
            git branch -vv | grep "origin/.*: gone]" | awk '{print $1}' | xargs git branch -D
        }
*NOTE*: The `source "..."` might be different than what is above. Copy/paste it from the brew installation output.

2. For a fast command line way to browse the most recent git commits: `$ brew install tig`. Then run `$ tig` at the command prompt.
3. For Code Editing: [RubyMine from JetBrains](https://www.jetbrains.com/ruby/download)

### Rubymine Support for Rubocop (Code Linting)
Code Linting gives formatting and syntax suggestions to make your code more readable.

In Rubymine go to `Rubymine` -> `Settings` -> `Editor` -> `Inspections` -> `Ruby` -> `Gems and gems management` -> `Rubocop`: Make sure that the checkbox is checked. Set the "Highlighting in editor:" settings and severities mapping for Refactor and Convention to "Warning".

## GIT

### Beginner's Guide to Changing Code

Every time you are ready to start work, do the following terminal commands in the wyzyr directory:

    $ git fetch
    $ git pull origin master
    $ bundle
    $ rails db:migrate
Then if your server isn't started yet:

 1. Tab 1: `$ rails s`
 2. Tab 2: `$ redis-server`
 3. Tab 3: `$ sidekiq -C 'config/sidekiq.yml'`


At this point you can point your browser to http://localhost:3000/ and start development work.
To stop the server click `CNTL-C` in all three tabs.

To check to make sure your code changes didn't break anything critical:

    $ rspec

Green dots are good, red F's are bad. Note that sometimes other people may have broken the build, so use your best judgement if the automated test errors were caused by your code or not (for example if you undo changes and re-run the test).

To push your code changes to the repo:

        $ git add .
        $ git commit -m "CP-XXX: Message describing what changes you made"
        $ git push origin branch_name

Note: replace XXX with the Jira story ID (very important).

Switching between master and a private branch:

        $ git checkout branch_name
        $ git checkout master

### Cherry Picking
If you need to copy over a commit from one branch to another without merging:

1. Copy the SHA of the commit you want to copy over (the program "tig" is good for this which can be installed via brew on a Mac).
2. Go to the branch you want to copy the commit to ($ git checkout [BRANCHNAME])
3. Use cherry-pick to copy over the commit:

        $ git cherry-pick [SHA]

NOTE: If you have more than one commit to copy over, do the cherry-pick commands in the same order as they were commited.
Also be careful about doing this if there is a high possibility of there being a conflict.
See https://ariejan.net/2010/06/10/cherry-picking-specific-commits-from-another-branch/

---

## 🖥️ New Development Machine Install (Windows 10 with WSL)

> **NOTE:** After installing something new, if it doesn't work as expected, try restarting your terminal.

### 📌 Prerequisites

- Windows 10 updated to the latest version.
- Administrator privileges.

---

### 1️⃣ Install WSL

1. Open **PowerShell** or **Command Prompt** as Administrator.
2. Run the following command to install WSL:
   ```bash
   wsl --install
   ```
3. Restart your computer.
4. After restarting, WSL will complete the installation. Create a UNIX username and password when prompted.  
   💡 *Keep your credentials simple (e.g., `dev`), to avoid confusion later.*
5. Verify the WSL installation:
   ```bash
   wsl --list --verbose
   ```
   ```
   Example output:
   Wsman Shell commandLine, version 0.2.1
   ```
6. For more details, see the [WSL documentation](https://learn.microsoft.com/en-us/windows/wsl/install).

---

### 2️⃣ Install RVM (Ruby Version Manager)

1. Open **WSL/Ubuntu terminal**.
2. Install **GPG** and import the required keys:
   ```bash
   sudo apt update
   sudo apt install gnupg -y

   gpg --keyserver keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
   ```
   💡 *GPG keys ensure a secure RVM installation.*
3. Install dependencies:
   ```bash
   sudo apt-get install software-properties-common
   ```
4. Add the RVM repository and install RVM:
   ```bash
   sudo apt-add-repository -y ppa:rael-gc/rvm
   sudo apt-get update
   sudo apt-get install rvm
   ```
5. Add your user to the RVM group ($USER will automatically insert your username):
   ```bash
   sudo usermod -a -G rvm $USER
   ```
   💡 *Verify by running `groups $USER`. If RVM doesn’t appear, close your WSL/Ubuntu terminal and open it again. This
   will reload your session and apply the group changes.*
6. Activate RVM:
   ```bash
   source /etc/profile.d/rvm.sh
   ```
7. Verify the installation:
   ```bash
   rvm -v
   ```
8. For more details, see the [RVM documentation](https://rvm.io/rvm/install).

---

### 3️⃣ Install Ruby

1. List available Ruby versions:
   ```bash
   rvm list known
   ```
2. Install the Latest Stable Version:
   ```bash
   rvm install ruby
   ```
3. Verify the installation:
   ```bash
   ruby -v
   ```
4. Install a Specific Ruby Version:
   ```bash
   rvm install ruby-<version>
   ```
   ```
   For example:
   rvm install ruby-3.2.6
   ```
5. Set the installed version as default:
   ```bash
   rvm use ruby-<version> --default
   ```
   ```
   For example:
   rvm use ruby-3.2.6 --default
   ```
   💡 *This sets Ruby 3.2.6 as your default version.*
6. Check the list of installed Ruby versions:
   ```bash
   rvm list
   ```
   ```
   Example output: 
   =* ruby-3.2.6 [ x86_64 ]
   ruby-3.4.1 [ x86_64 ]
   
   # => - current
   # =* - current && default
   #  * - default 
   ```

---

### 4️⃣ Install Node.js

1. Remove old versions (if any):
   ```bash
   sudo apt remove nodejs
   ```
2. Add the **NodeSource** repository:
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
   ```
3. Install Node.js:
   ```bash
   sudo apt install -y nodejs
   ```
4. Verify the installation:
   ```bash
   node -v
   ```

---

### 5️⃣ Install Yarn

1. Add the Yarn repository key:
   ```bash
   curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo tee /etc/apt/trusted.gpg.d/yarn.asc
   ```
2. Add the Yarn repository:
   ```bash
   echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
   ```
3. Update your package list:
   ```bash
   sudo apt update
   ```
4. Install Yarn:
   ```bash
   sudo apt install yarn
   ```
5. Verify the installation:
   ```bash
   yarn -v
   ```

---

### 6️⃣ Install Ruby on Rails

1. Install Rails using RubyGems:
   ```bash
   gem install rails
   ```
2. Verify the installation:
   ```bash
   rails -v
   ```
   💡 *Install the latest stable version for compatibility.*

---

### 7️⃣ Set Up PostgreSQL for WSL/Ubuntu

1. Install PostgreSQL:
   ```bash
   sudo apt update
   sudo apt install postgresql postgresql-contrib -y
   ```
2. Verify the existence of the `postgres` user:
   ```bash
   getent passwd postgres
   ```
3. Ensure the PostgreSQL service is running:
   ```bash
   sudo service postgresql status
   ```
   💡 *Ensure it's always running when working with Rails projects.*

4. Create a new PostgreSQL user:
   1. Connect to PostgreSQL:
      ```bash
      sudo -u postgres psql
      ```
   2. Create a new user:
      ```sql
      CREATE ROLE <new_user_name> WITH LOGIN PASSWORD 'your_password';
      ```
   3. Grant privileges:
      ```sql
      ALTER ROLE <new_user_name> WITH CREATEDB REPLICATION CREATEROLE BYPASSRLS;
      ```
   4. Verify the role:
      ```sql
      \du
      ```
   5. Exit the `psql` shell:
      ```sql
      \q
      ```

---

### 8️⃣ Clone the Repository and Set Up the Project

1. Open WSL/Ubuntu terminal.
2. Create a base folder for all your RoR projects:
   ```bash
   mkdir ~/projects
   ```
   💡 *Use this folder for all your RoR projects.*
3. Verify if the folder was created:
   ```bash
   ls
   ```
4. Navigate to your `/projects` directory:
   ```bash
   cd ~/projects
   ```
5. Clone the repository:
   ```bash
   git clone git@github.com:my_github_account/my_repo.git
   ```
6. Enter the project directory:
   ```bash
   cd my_repo
   ```
7. Install Bundler:
   ```bash
   gem install bundler
   ```
   💡 *Bundler helps manage dependencies in your Ruby projects.*
8. Update RubyGems:
   ```bash
   gem update --system
   ```
9. Install dependencies:
   ```bash
   bundle install
   yarn install
   ```
10. Create the database:
   ```bash
   rails db:create
   ```
11. Start the Rails server:
   ```bash
   rails s
   ```
12. Open [http://localhost:3000](http://localhost:3000) in your browser to view the app.

> **NOTE:** Always remember to store all your RoR projects in the `~/projects` directory inside the WSL environment
> (\\wsl.localhost\Ubuntu\home\UNIX_username\projects). This practice avoids file system conflicts between Windows and
> Ubuntu, ensuring your development environment runs smoothly.
---

### 🛠️ Maintenance and Updates

1. Regularly update packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Manually update Linux distributions:
   ```bash
   wsl --update
   ```

💡 *Windows does not automatically update Linux distributions. Handle this manually as needed.*

---

### Git Workflow Tips

1. Fetch and pull the latest changes before starting:
   ```bash
   git fetch
   git pull origin master
   bundle install
   rails db:migrate
   ```
2. Start the server and background workers:
   ```bash
   rails s
   redis-server
   sidekiq -C config/sidekiq.yml
   ```
3. Test your changes using RSpec:
   ```bash
   rspec
   ```
4. Commit and push changes:
   ```bash
   git add .
   git commit -m "CP-XXX: Description of changes"
   git push origin branch_name
   ```

---

### Optional: Removing All Ruby Versions

1. View the list of installed Ruby versions:
   ```bash
   rvm list
   ```
2. Remove all versions:
   ```bash
   rvm list strings | xargs -n 1 rvm remove
   ```
3. Ensure the list is empty:
   ```bash
   rvm list
   ```

### Optional: Removing RVM

1. Remove RVM:
   ```bash
   rvm implode
   ```
2. Manually delete any remaining files:
   ```bash
   rm -rf ~/.rvm
   ```
3. Verify RVM is no longer available:
   ```bash
   rvm
   ```
   If the command is not found, the removal was successful.

---

### Optional: Resolving error ‘ruby\r’: No such file or directory

This error occurs when Windows-style carriage return (\r\n) characters are present in script files (e.g., bin/rails,
bin/dev) instead of Unix-style line endings (\n). Follow the steps below to resolve the issue:

1. **Convert Files to LF Format:**
   Ensure all files in the `bin` folder use Unix-style line endings:
   ```bash
   dos2unix bin/*
   ```
2. **Verify File Format:**
   Confirm that the files are now in the correct format:
   ```bash
   file bin/*
   ```
   You should see `ASCII text` without `CRLF`.
   ```bash
   Example:
   bin/rails: ASCII text
   bin/dev: ASCII text
   ```
3. **Check File Permissions**
   Ensure the files in the bin folder are executable:
   1. Verify the permissions for a specific file (e.g., bin/dev):
    ```bash
    ls -la bin/dev
    ```
   If the output shows no x (execution) in the permissions (e.g., -rw-r--r--), add execution permissions:
    ```bash 
    chmod +x bin/dev
    ```
   2. Alternatively, make all scripts in the bin folder executable:
    ```bash
    chmod +x bin/*
    ```
4. **Restart the Script**
   Run the script again to check if the error is resolved:
   ```bash
   bin/dev
   ```

> **NOTE:** If you use RubyMine IDE, this issue can often be resolved by configuring line separators in the
> IDE: [Configure line separators in RubyMine](https://www.jetbrains.com/help/objc/configuring-line-endings-and-line-separators.html)
---

### Optional Tools

1. Git Commit Browser: Install `tig`:
   ```bash
   sudo apt install tig
   ```
   Run `tig` to browse recent commits.

2. IDE Recommendation: Use [RubyMine](https://www.jetbrains.com/ruby/download) for code editing.

3. Linting: Set up RuboCop for code quality checks in RubyMine under
   `Settings > Editor > Inspections > Ruby > Gems and gems management > RuboCop`.
   Make sure that the checkbox is checked. Set the "Highlighting in editor:" settings and severities mapping for
   Refactor and Convention to "Warning"

---

Happy coding 🚀