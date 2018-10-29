# Interacting with GIT in Open Saltstack

### Configuring the SS master
* Leave the master file as default
* Install **pygit2** or **GitPython** (pygit2 usually doesn't recognize the URL or the branch)
  * `sudo apt-get install -y python3-pygit2` or 
  `sudo apt-get install -y python3-git` or 
  `sudo apt-get install -y gitpython` (google it)
 * edit/create a new `master_whatever.conf` file - name not important
  ```
  fileserver_backend:
  - git
  - roots

  gitfs_provider: [pygit2 or GitPython]

  git_pillar_provider: [pygit2 or GitPython]
  
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
    - https://github.com/dragosstan2602/git_pillar.git
  
  # NO CREDENTIALS NEEDED - PUBLIC REPO
  ext_pillar:
    - git:
      - master https://github.com/dragosstan2602/git_pillar.git:
        # ENV MUST MATCH THE ENV IN TOP.SLS
        - env: base
  ```
  * restart the salt-master -> `service salt-master restart`
  * optionally on the master server you can run the process in debug mode to check for errors ->
  `salt-master -l debug`