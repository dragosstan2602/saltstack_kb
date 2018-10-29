# Interacting with GIT in SS Enterprise Edition

### Configuring the SS master
* Leave the master file as default
* Install **pygit2** if using a private GIT repo - otherwise you'll get this error when starting the 
salt-master process
```
Oct 29 06:42:22 lx-00144 systemd[1]: Started The Salt Master Server.
Oct 29 06:42:22 lx-00144 salt-master[4118]: [CRITICAL] gitfs authentication was configured, but the 'gitpython' gitfs_provider does not support authentication. The providers for which authentication is supported in gitfs are: pygit2. See the GitFS Walkthrough in the Salt documentation for further information.
Oct 29 06:42:22 lx-00144 salt-master[4118]: [CRITICAL] git_pillar authentication parameter 'password' (from remote 'master http://githuben.intranet.mckinsey.com/NetDevOps/saltstack-states.git') is only supported by the following provider(s): pygit2. Current git_pillar_provider is 'gitpython'.
Oct 29 06:42:22 lx-00144 salt-master[4118]: [CRITICAL] git_pillar authentication parameter 'user' (from remote 'master http://githuben.intranet.mckinsey.com/NetDevOps/saltstack-states.git') is only supported by the following provider(s): pygit2. Current git_pillar_provider is 'gitpython'.
Oct 29 06:42:22 lx-00144 salt-master[4118]: [CRITICAL] Failed to load gitfs
Oct 29 06:42:22 lx-00144 salt-master[4118]: [CRITICAL] Failed to load git_pillar
Oct 29 06:42:22 lx-00144 salt-master[4118]: [CRITICAL] Master failed pre flight checks, exiting
Oct 29 06:42:22 lx-00144 systemd[1]: salt-master.service: main process exited, code=exited, status=1/FAILURE
Oct 29 06:42:22 lx-00144 systemd[1]: Unit salt-master.service entered failed state.
Oct 29 06:42:22 lx-00144 systemd[1]: salt-master.service failed.
```
* `yum install -y python-pygit2`

* go to `/etc/salt/master.d` and edit the `raas.conf` file:
  * Disable the `fileserver_backend` and `ext_pillar` settings:
  ```
  # Enable fileserver backends to use eAPI first, then local filesystem
  #fileserver_backend:
  #  - git
  #  - sseapi
  #  - roots

  # Enable the eAPI external pillar
  #ext_pillar:
  #  - sseapi: {}
  ```
  * edit/create a new `master_whatever.conf` file - name not important
  ```
  fileserver_backend:
  - git
  - sseapi
  - roots

  gitfs_provider: pygit2

  git_pillar_provider: pygit2
  
  # FOR HTTP CONNECTIONS MUST BE SET TO TRUE, NOT NEEDED FOR HTTPS
  git_pillar_insecure_auth: True

  # CAN BE REMOVED -TRY IT
  file_roots:
    base:
      - /srv/salt
  
  # CAN BE REMOVED -TRY IT
  pillar_roots:
    base:
      - /srv/pillar

  gitfs_remote:
    - http://githuben.intranet.mckinsey.com/NetDevOps/saltstack-states.git

  ext_pillar:
    - git:
      - master http://githuben.intranet.mckinsey.com/NetDevOps/saltstack-states.git:
        # ENV MUST MATCH THE ENV IN TOP.SLS
        - env: base
        - user: USERNAME
        - password: PASSWORD
    - sseapi: {}
  ```
  * restart the salt-master and salt-enterprise processes
  ```
  service salt-master restart -> on the master server
  service raas restart -> on the enterprise GUI server
  ```
  * optionally on the master server you can run the process in debug mode to check for errors
  
  `salt-master -l debug`