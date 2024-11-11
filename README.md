
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
12. Change your directory to where you want your work projects in terminal and clone the git repo: `$ git clone git@github.com:wahanegi/wyzyr.git`
13. Go into the directory `$ cd wyzyr`. Confirm that when you run `$ rvm gemset list` it lists "wyzyr" as your gemset.
14. Run `$ gem install bundler`
15. Run `$ gem update --system`
16. Run bundler: `$ bundle`
17. Install redis using homebrew: `$ brew install redis`
18. Install Yarn `$ brew install yarn`
19. Install Node `$ brew install node`
20. Install Webpack dependencies `yarn install`. If you run into problems doing the [yarn issues](#yarn-issues) section below.
21. Create a new database: `$ rails db:create`
22. Install the new Heroku CLI: `$ brew tap heroku/brew && brew install heroku`.
23. Log into your Heroku account: `$ heroku login`
24. Download a copy of a [recent database dump](https://drive.google.com/drive/u/1/folders/1AD4GvxG6WJPEOwfjFvrdsHE_SmMTAJA7) and then install it locally: `$ rake db:import`
25. Should be ready to roll: `$ rails s`
26. In Rubymine go to `Rubymine` -> `Settings` -> `Languages & Frameworks` -> `Ruby SDK and Gems`: Make sure it has "RVM: ruby-3.2.4" with the `wyzyr` option selected.
27. Now switch to `Javascript`: Make sure that `Javascript language version` and make sure it is set to `ECMAScript 6+`.

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

Green dots are good, red F's are bad. Note that sometimes other people may have broken the build, so use your best judgement if the automated test errors were caused by your code or not (for example if you undo changes and re-run the test). You can also compare your local errors to that on our CI server at https://circleci.com/gh/wahanegi/wyzyr.

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

## Code Submissions and Reviews

1. Any significant feature should be done in a separate branch.
   
         $ git checkout -b CP-XXX
2. When a feature is complete and tests pass, push to github.

         $ git push origin CP-XXX
3. If you want to update your new branch with changes from master (very recommended), do the following:
   
         $ git checkout master
         $ git pull origin master
         $ git checkout CP-XXX
         $ git merge master
   Then resolve conflicts manually and push to your new_branch_name again
   
         $ git push origin CP-XXX

4. When all work in branch is done, create a pull request:
    - Go to https://github.com/wahanegi/wyzyr
    - Click "Pull Requests" -> "New Pull Request"
    - Set base branch to master and compare-to branch to the `CP-XXX` branch where you've done your work
    - Click "Create pull request"
    - Add a description of the pull request and start the pull request description with the story or epic ID, `CP-XXX`


## Working With Javascript

We use Shakapacker with Babel to compile ES6 javascript code. This code placed in `app/javasript/src`. All old style javascripts (ES5) placed in `app/assets/javascript`.

### Using Hot Module Replacement mode to speed up development
We use Hot Module Replacement(HMR) mode to have javascript code recompiled faster. To use it run the following command in a separate terminal window: `$ bin/shakapacker-dev-server`.

### Yarn Issues
When things don't work on the front end (for example everything on a page is blank) try `yarn install --check-files`.
If that doesn't work then try `yarn upgrade`. Updates to `yarn.lock` should be tested by QA before being pushed to the
production server.

**ATTENTION:** Normally heroku uses last LTS NodeJS version, so if you run
into problems when doing `yarn install...` or `yarn upgrade` commands locally.

      ~/workspace/wyzyr(CP-2325)$ yarn install --check-files
      yarn install v1.22.19
      [1/6] 🔍  Validating package.json...
      error @: The engine "node" is incompatible with this module. Expected version "18.16.1". Got "19.1.0"
      error Found incompatible module.
      info Visit https://yarnpkg.com/en/docs/cli/install for documentation about this command.

To fix this issue, please run:

      $ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
      $ source ~/.nvm/nvm.sh
      $ nvm install 18.16.1
      $ nvm use 18.16.1

## Heroku

### Deploy Setup

1. Make sure the heroku toolbelt is installed from https://toolbelt.heroku.com/
2. Restart terminal and cd into the wyzyr directory: `$ cd wyzyr`
3. Run `$ heroku login` and do the authentication into your account.
