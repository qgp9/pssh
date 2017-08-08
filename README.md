# pssh
A script to manage ssh/config

## TL;DR
1. Put a configuration file at  `~/.ssh/pssh/.ssh-config`
2. Install it : `pssh install`

### Short example
```
## Variable
$DO_PKEY=~/.ssh/key/do/d1_id_rsa
$VPS_COMMON_OPTION = {
  PubkeyAuthentication yes
  Protocol 2
  ForwardX11 no
}

@@ GITHUB : Every Comment will be conserved;
+---------------+------------------+---------------------------------------+ # This is dummy lines, be ignored.
| github-user1  |  git@github.com  | ~/.ssh/key/github/github_user1_id_rsa | # This comment will be placed before this line.
| github-user2  |  git@github.com  | ~/.ssh/key/github/github_user2_id_rsa |
+---------------+------------------+---------------------------------------+ # This is dummy lines, be ignored.


@@ DO at amsterdam
+-------+---------------------------+------------+
| d1    | user1@192.168.1.101       | $DO_PKEY   | $VPS_COMMON_OPTION
| d2    | user1@192.168.1.102       | $DO_PKEY   | $VPS_COMMON_OPTION
+-------+---------------------------+------------+
# Without pkey but with variabled option
+-------+---------------------------+------------+
| d4    | user1@192.168.1.101       |   x        | $VPS_COMMON_OPTION  # "x" should be instead of empty space.
+-------+---------------------------+------------+

+-------+---------------------------+------------+
| d4    | user1@192.168.1.101       |            | # You don't need "x" without 4th field.
          $VPS_COMMON_OPTION
          ForwardX11 yes # .ssh/config format is always avaiable in the place
+-------+---------------------------+------------+

# Usual .ssh/config format is available
Host *
Protocol 2
IdentityFile ~/.ssh/key/id_rsa
ControlMaster auto
ControlPath ~/.ssh/controlmasters/%r@%h:%p
ControlPersist 1h

## Usage
* All files are under ~/.ssh/pssh ( essentialy just a .ssh-config )
```
$ pssh -h
# usage : pssh <command> <options> 

# Commands
    cat        : Print Pssh config file with grep
                 usage: pssh cat [string to grep]
    edit       : Edit Pssh config file
    install    : Install Pssh config file to /home/pung96/.ssh/config
    key_deploy : Deploy pub key to ssh server : pssh key_deploy <server> <file>
    list       : Host List
                 usage: pssh list [string to grep] [-g [group index]] [-G [grep in groups]]
                 -g withoud 'group index' will list only a group list
                 -G will grep group names and inline comments and print with it's hosts
    path       : Print Path of pssh directory
    test       : Compile /home/pung96/.ssh/pssh/.ssh_config and print on screen

# Options
    -c --config          : Use custom pssh config file
    -h --help            : Display Help
    -b                   : display without color
    -g --group           : group option for list
    -G                   : group option for list
```
  * NOTE : `edit` doesn't mean installing after edit. One needs additional call of `pssh install` to apply changes.

* Bash completion
```
echo 'eval $(pssh bash_completion_template)' >> ~/.bash_profile
```
  * NOTE : You may need an additional path in advance.

## Descriptions

The configuration formate is super-set of `.ssh/config` format. So you can put every raw configurations in `.ssh-config`

But if any line begin with `|`, `$` ,`+` or `-`, they will be translated by `pssh` formater.

## Rules
* A line begins with

  | Begins with | RegExp | Descriptions |
  |:-----------:|:------:|:-------------|
  | `+`     | `/^\s*\+/` | Ignore a line. |
  | `\|`    | `/^\s*\|/` | Parse a line as an `entry` |
  | `$`     | `/^\s*\$/` | Parse a line as `variable` (both of definition or using) |
  | `-`     | `/^\s*\-/` | Remove until `-` and leave a line there |
  | `@@`    | `/^\s*@@/` | Group |
  | others  |            | Just leave it as it is |


  White spaces before triggers are allowed

* Variable
  * If a `variable` comes before `=` in a line ( `/^\s*\$.*=/`), it's a `definition`
  * Otherwise a `using`, so just translated to a content
* Entry
  * After first `|`, all following `|`s are dealt as spaces
  * Include first `#`, all followings are `comments`. They will be placed before the line.
  * fields are `| alias | user@server:port | key | options`


## Example of `.ssh-config`
* more on 
	https://github.com/qgp9/pssh/blob/master/example/.ssh_config
* and compiled `config` by `pssh test`
  https://github.com/qgp9/pssh/blob/master/example/config



```
