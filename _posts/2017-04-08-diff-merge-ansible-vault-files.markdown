---
layout: post
title: Diff and merge ansible vault files
---

[Ansible](https://www.ansible.com) is a great tool for all kinds of automations. Very simple and intuitive. We use it with  [playbooks](http://docs.ansible.com/ansible/playbooks.html) and [ansible-vault](http://docs.ansible.com/ansible/playbooks_vault.html) to generate and propagate configuration files across our servers, both on production and staging/development environments ([because config is a place to separate credentials between environments](http://www.schneems.com/2017/03/21/config-behavior-versus-credentials)).

Thanks to vault all data, even sensitive one such as private keys and passwords to 3rd services, can be stored in source control.

**DIFF**

Tracking changes in raw vault files can be a pain, though. Files are encrypted so even the slightest change will generate big, meaningless diff.

```
$ANSIBLE_VAULT;1.1;AES256
 -34343234386466633034356430356166383261633938396537323964326631626463376238323365
 -3739613134633331303362633266616138663765626134620a373830396434313230336161343766
 -31333235613165333763386139336366633436373963663634626466336330376364393730653862
 -3930376461303738650a383463353261613064663139663163316632376666303233343263333364
 -37636637376538376637646161656361346534623262626163663932313432363464393635373835
 +32643265363939363132656265356361623030633239633837306530306437653965643963323037
 +3964663334303535323734393731663433613437313765340a636334373332616330323730366238
 +65363063653631346230623634356238653165396661343534346530643061333032626434666235
 +6532646662366632350a353638646161393663353037363366643638313138316533663261333864
 +36356365393166636537653438616165616635323065356361653963326639313565626361323866
 +39353362353065353962383035663530636539656233663761613832376566363239303433336530

```

It is even worse when someone edits that file while you were on a branch. Git will see two entirely different contents and always marks it as a conflict. To resolve it and determine what exactly has changed you need to decrypt both files and apply the patch manually. It can be fun for the first time but in a long run it is annoying.

Fortunately, you can automate this process and tell git to decrypt those files before diffing them. First of all you should specify which files you are referring to. Use [gitattributes](https://book.git-scm.com/docs/gitattributes) for that and mark YAML files in `devops/vars` directory.

```
devops/vars/**/*.yml diff=ansible-vault
```

`ansible-vault` is name for `diff driver`. Next in [gitconfig](https://git-scm.com/docs/git-config) configure how this driver should behave.

```
[diff "ansible-vault"]
  textconv = ansible-vault view
  cachetextconv = false
```

In `textconv` paste command that the diff driver should call to generate the human-readable file. `cachetextconv` is set to `false` because you don’t want git to cache your secrets.

After these changes git shows standard diff although files are encrypted.

**MERGE**

Only oneway conversion can be set with `diff driver`. So for merge you need to use something else. A low-level custom merge driver.

Firstly, mark files which should use this driver.

```
devops/vars/**/*.yml diff=ansible-vault merge=ansible-vault
```

Change it in the same place as for `diff driver` and give it same name. Now let’s configure this new driver.

```
[merge "ansible-vault"]
  name = ansible-vault merge driver
  driver = /usr/local/bin/ansible-vault-merge -- %O %A %B %P
```

In `driver` put path to command that implements new merge strategy. Here it is.

```
#!/bin/sh

BASE=$1
CURRENT=$2
OTHER=$3
LOCATION=$4

set -e

ansible-vault decrypt $BASE > /dev/null
ansible-vault decrypt $CURRENT > /dev/null
ansible-vault decrypt $OTHER > /dev/null

if ! git merge-file -L CURRENT -L BASE -L OTHER $CURRENT $BASE $OTHER; then
  ansible-vault encrypt $CURRENT
  exit 1
else
  ansible-vault encrypt $CURRENT
fi
```

Standard `merge-file` mechanism is used, but it operates on encrypted files. If git encounters a conflict it will also be visible in the file.

With these two simple changes you can use ansible-vault with version control system as it would be a plain file. Please let me know in the comments how you deal with the ansible-vault files in your workflow.
