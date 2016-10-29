# pssh
A script to manage ssh/config

## TL;DR
* configuration file :  `~/.ssh/.ssh-config`
* compile : `pssh -i`

## Short Description

The configuration formate is super-set of `.ssh/config` format. So you can put every raw configurations in `.ssh-config`

But if any line begin with `|`, `$` or `-`, they will be translated by `pssh`  formater. 


## Example of `.ssh-config`
* more on https://github.com/qgp9/pssh/blob/master/example/.ssh_config


```
## Variable
$DO_PKEY=~/.ssh/key/do/d1_id_rsa
$VPS_COMMON_OPTION = {
  PubkeyAuthentication yes
  Protocol 2
  ForwardX11 no
}

## GITHUB : Every Comment will be conserved;
+---------------+------------------+---------------------------------------+ # This is dummy lines, be ignored.
| github-user1  |  git@github.com  | ~/.ssh/key/github/github_user1_id_rsa | # This comment will be placed before this line.
| github-user2  |  git@github.com  | ~/.ssh/key/github/github_user2_id_rsa | 
+---------------+------------------+---------------------------------------+ # This is dummy lines, be ignored.


# DO at amsterdam
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
```
