# Working with Jinja templates when applying config

## Pre Requisites
* Proxy minion configured and registered with the master
* Napalm installed on the proxy minion

## Offline templates
* place a jinja template into a folder on the proxy - ex: `/home/user/template.jinja`
```
{% set hostname = grains.get('hostname') -%}

hostname {{hostname}}-NEW
```

* run the salt command on a glob level (test and debug are optional)
`salt '*' net.load_template template.jinja template_path='/home/user/' debug=True test=True`

* or place the jinja template in one of the paths mentioned in `file_roots` on the master and use:
`salt://ss_cfg_template.jinja` in the command to specify the template/path

## Online (HTTP/HTTPS) templates
ex: https://raw.githubusercontent.com/dragosstan2602/git_pillar/master/config_templates/ss_cfg_template.jinja
* If using GitHub make sure to add the **raw URL**

* Run the command:
`salt -C "G@osfullname:proxy and G@hostname:CSR* or G@hostname:vEOS*" net.load_template https://raw.githubusercontent.com/dragosstan2602/git_pillar/master/config_templates/ss_cfg_template.jinja debug=True test=True skip_verify=True`