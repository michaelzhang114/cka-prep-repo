# Local environment setup

## Multipass

Launch a new Ubuntu node `vm1`
`multipass launch -n vm1 -c 4 -m 2G -d 20G`

Open a shell for that node
`multipass shell vm1`

Set a password for the default `ubuntu` user
`sudo passwd ubuntu`

Now you can login using Hyper-V w/ username `ubuntu`
