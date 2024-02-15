ansible-role-nss
=========

This role will prepare a development environment for NSS.
It is based in the official documentation and daily experiences.
This role will be in continually improvement since now! Any help is welcome!

If you are new with NSS, this Ansible Role will be your best friend to get off to a good start.
If you are an experienced developer, maybe some approaches here will not attend you.
But, at least, you may get some tips or, even better, suggest improvements. : )

It was developed and tested in a Fedora environment, but should work fine
with CentOS and RedHat too.

For Debian and Ubuntu are necessary some adjusts related to packages, repos
and environment files. If you have some time to test and write the adjusts or
at least report the necessary adjusts for other distributions, please post in the
official repo (GitHub). Every user of this role will thank you! : )

This role will:
- Install all packages necessary to develop and collaborate with the NSS project.
- Create the folders MOZILLA and BUGZILLA in your home directory.
- Download nss, nspr and nss-tools repositories in MOZILLA folder and update them.
- Download and maintain updated all the necessary auxiliary repositories.
- Set the necessary local environment variables.
- Build NSS with GYP to validate all the environment.
- Configure the Mercurial and enable some nice extensions.
- Set the VSCODE repository, install it and some nice extensions.
- Create a development workspace for each BUG informed in vscode_workspaces list.
	- The repositories are cloned from the main repository in MOZILLA folder.
- Set the folder workspaces in VSCode, according with vscode_workspaces list.
- Make the necessary adjusts for specific OSs.

It is strongly recommended to reading this documents, in this order, before start coding with NSS:
- https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Building
- https://developer.mozilla.org/pt-BR/docs/Mozilla/Projects/NSS/NSS_Sources_Building_Testing
- https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Introduction_to_Network_Security_Services
- https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/NSS_API_GUIDELINES
- https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Coding_Style

Daily Development
------------

hg pull
- Verify and download the last commits/changesets from repository.

hg update
- Change your current changeset for the tip/last changeset.
- Useful if you don't have some local modification.

hg log -G
- Nice option to see a tree of changesets.

hg log -r <changeset> -p
- Show all the files and respective modifications in the changeset.
- Useful to see wat was modified in a specific commit.

hg rebase -s <source changeset> -d <destination changeset>
- This command is very useful before send patches to Phabricator.
- The source changeset is your last commit for the related patch.
- The destination changeset is the last changeset in the repo, the tip changeset.

hg commit --amend -m "Bug <BugID> - Bug or commit description. r=reviewer1,reviewer2"
- --amend will ensure your commits are cumulative and don't create a mess.
- -m is the description of that commit. The syntax above is important to a better automation.

.nss/build.sh [options]
- Use this command after some modifications, to ensure the build is ok.

.nss/tests/gtests/gtests.sh
- This example run only the gtests.
- You can also call the all.sh, but this is very long to call all the time.
- Explore the tests options and scripts. Every code should be tested!!
- You can be more specific, like this: ../dist/Debug/bin/ssl_gtest -d . -w --gtest_filter"*TestName*"

.nss/mach clang-format <modified file>
- We have to check the clang-format before sent patches to Phabricator.
- This command will load a docker container with a specific clang-format version to verify
  the <modified file>
- https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Coding_Style/Formatting_C++_Code_With_clang-format

moz-phab
- This command should be used when your patch is ready for review.
- It will automate the process of send your commit to Phabricator.
- If the commit message is right, usually you only need to confirm.
- This command will return a link for your revision.
- https://github.com/mozilla-conduit/review

arc diff --preview
- After receive a review feedback, may be necessary to update your patch.
- Make the necessary modifications, commit locally and use this command to generate a preview.
- This preview will be in Phabricator and with the generated link you can update a review.

~/MOZILLA/nss-tools/nss-try.sh -p all -u all -t all
- If you have permissions to send the patch for try-server, this is the command.
- In this example we are doing all the tests. Sometimes this complete test report a problem
  and you can try again just the test related to the found problem.
- Explore the options of nss-try.sh and take a look in this link:
  - https://wiki.mozilla.org/NSS:TryServer

Requirements
------------

- python3
- Internet Access to sync repositories.

We will prepare the technical environment, but you have to create your credentials:
- 1 - Bugzilla - https://bugzilla.mozilla.org/
	- To colaborate with bugs.
- 2 - Phabricator - https://phabricator.services.mozilla.com/ 
	- To send and review patches.
- 3 - Optional Permissions:
	- To edit bugs.
	- To push to try-servers.

Role Variables
--------------

You can customize your environment in a very simple and centralized way editing some variables in:
- defaults/main.yml

In some rare cases, you may change some configuration to reflect your local environment in:
- vars/*.yml

IMPORTANT variables to set before the first use:
- defaults/main.yml: hg_username
- defaults/main.yml: git_username
- defaults/main.yml: nss_try

Observe that the above variables could be set in your playbook too, which, IMO is much more elegant. ; )
Take a look in the Example Playbook section.

Dependencies
------------

None

Example Inventory
----------------

You can see a complete example inventory file inside the "files" folder.

Example Playbook
----------------

This playbook will prepare everything with the right variables. For this example, lets call
this playbook file as "ansible_nss.yml" (an example file is in "files" folder):

---
- hosts: linux
  vars:
    - hg_username: 'Marcus Burghardt <myemail@mydomain.com>'
    - git_username: 'Marcus Burghardt <myemail@mydomain.com>'
    - nss_try: 'ssh://myemail@mydomain.com@hg.mozilla.org/projects/nss-try'
  roles:
    - ansible-role-nss

Considering the inventory file is in the same folder and is called "hosts_nss", you can run
this command to see the magic happen:
$ ansible-playbook -K -i hosts_nss ansible_nss.yml

Maybe you would like to set some ansible configurations for this environment.
For instance, define a local folder to hold downloads roles. You can find an example of
ansible.cfg file in "files" folder.

License
-------

This Source Code Form is subject to the terms of the Mozilla Public
License, v. 2.0. If a copy of the MPL was not distributed with this
file, You can obtain one at http://mozilla.org/MPL/2.0/.

Author Information
------------------

- Marcus Burghardt
- https://github.com/marcusburghardt
- https://www.linkedin.com/in/marcusburghardt/
