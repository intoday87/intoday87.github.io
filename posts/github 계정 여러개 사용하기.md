- mac에서 github 계정 여러개 사용하기
    - keychain에서 [github.com](http://github.com)이 등록됨에 따라 여러 [github.com](http://github.com) 계정을 사용하지 못하고 keychain에 들어간 계정만 사용할 수 있게 되는데 ssh 키 등록을 통해 해결할 수 있다
	
```
Step 1 - Create a New SSH Key
-----------------------------
We need to generate a unique SSH key for our second GitHub account.

`ssh-keygen -t rsa -C "your-email-address"`

Be careful that you don't over-write your existing key for your personal account. Instead, when prompted, save the file as `id_rsa_COMPANY`. In my case, I've saved the file to `~/.ssh/id_rsa_work`.

Step 2 - Attach the New Key
---------------------------
Next, login to your second GitHub account, browse to **"Account Overview"** and attach the new key, within the **"SSH Public Keys"** section. To retrieve the value of the key that you just created, return to the **Terminal** and type: `vim ~/.ssh/id_rsa_COMPANY.pub.`

Copy the entire string that is displayed, and paste this into the GitHub textarea. Feel free to give it any title you wish.

Next, because we saved our key with a unique name, we need to tell SSH about it. Within the **Terminal**, type: `ssh-add ~/.ssh/id_rsa_COMPANY`. If successful, you'll see a response of `Identity Added.`

Step 3 - Create a Config File
-----------------------------
We've done the bulk of the workload; but now we need a way to specify when we wish to push to our personal account, and when we should instead push to our company account. To do so, let's create a config file.

`touch ~/.ssh/config vim config`

If you're not comfortable with Vim, feel free to open it within any editor of your choice. Paste in the following snippet.

Default GitHub
**************
		Host github.com
		  HostName github.com
		  User git
		  IdentityFile ~/.ssh/id_rsa
This is the default setup for pushing to our personal GitHub account. Notice that we're able to attach an identity file to the host. Let's add another one for the company account. Directly below the code above, add:

		Host github-COMPANY
		  HostName github.com
		  User git
		  IdentityFile ~/.ssh/id_rsa_COMPANY

This time, rather than setting the host to github.com, we've named it as github-COMPANY. The difference is that we're now attaching the new identity file that we created previously: `id_rsa_COMPANY`. Save the page and exit!

Step 4 - Try it Out
-------------------
It's time to see if our efforts were successful. Create a test directory, initialize git, and create your first commit.

		git init
		git commit -am "first commit'
Login to your company account, create a new repository, give it a name of "Test," and then return to the Terminal and push your git repo to GitHub.

		git remote add origin git@github-COMPANY:Company/testing.git
		git push origin master
Note that, this time, rather than pushing to git@github.com, we're using the custom host that we create in the
config file: git@github-COMPANY.

Return to GitHub, and you should now see your repository. Remember:

When pushing to your personal account, proceed as you always have.
For your company account, make sure that you use `git!github-COMPANY` as the host.
```
출처: https://gist.githubusercontent.com/JoaquimLey/e6049a12c8fd2923611802384cd2fb4a/raw/12f4a1b95bfdbc77ea0ac993e63b8e459173c3db/github_multiple-accounts.md

`$ git remote set-url origin git@github-intoday87:intoday87/next-amplified.git` 기존 repo에는 origin 주소를 ssh git 주소로 변경해준다. `github-intoday87` 은 git ssh config에 있는 host로 적어놓은 이름
