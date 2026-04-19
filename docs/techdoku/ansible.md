# ansible

## inventory

global: `/etc/ansible/hosts' # Wenn kein anderes Inv. explizit def. wird

Normalerweise beim Projekt (z.B. `hosts.ini`).

Spezifizieren des inventory mit `inventory = hosts.ini` in der `[defaults]`-Sektion von `./ansible.cfg`

**Übersteuern / manuelles Spezifizieren des Inventory-Files**

* `ansible -i <pfad/zu/inventory>`
* `export ANSIBLE_INVENTORY=<pfad/zu/inventory>`

## Targeting

### Snippets

**ad-hoc**

```
ansible app ... --limit "*4" # Wildcard-Matching
ansible db ...  --limit ~".*\.4" # regex-Matching
ansible multi -b -m apt -a "upgrade=dist update_cache=yes"
ansible multi -b -m shell -a "tail /var/log/messages | grep ansible-command" # komplexere Shell-Commands
```

**ansible-playbook**

```
ansible-playbook playbook.yml --limit webservers # selektiv eingrenzen
ansible-playbook playbook.yml --list-hosts # anzeigen auf welchen Hosts das Playbook laufen würde
ansible-playbook playbook.yml --extra-vars=VARS # Variablen f. Playbook ("key=value,key=value")
```

## Nützliche modules

* **`apt`**\
apt
* **`package`**\
größtenteils identisch zu dnf, apt usw. -> unabhängig vom Paketmanager
* **`user`**\
z.B. `ansible <dst> -b -m user -a "name=sac group=xyz createhome=yes"`
* **`group`**\
z.B. `ansible &lt;dst> -b -m group -a "name=admin state=present"
* **`stat`**\
Infos über File (z.B. `ansible &lt;dst> -m stat -a "path=/etc/hosts"'
* **`copy`, `fetch`**\
Datei oder kleine Verz. auf/von Server kopieren
* **`file`**\
Dateien/Verz. erstellen (wie `touch`), permissions setzen (z.B. `ansible <dat> -m file -a "dest=/tmp/test mode=644 state=directory"`; oder löschen mit `... -a dest=/tmp/test state=absent`
* **`cron`**\
Management von Cronjobs
* **`git`**\
git checkouts, pulls usw.
* **`mysql_db`**\
MySQL
* **`get_url`**\
Download einer URL
* **`unarchive`**\
Archive (kann auch Download wie `get_url` mitmachen)

## Playbooks

### Commands

Commands über das `command`-Modul haben u. a. Erweiterungen wie `creates` (führt cmd nur aus wenn ein File vorher nicht existiert) oder `removes` (führt cmd nur aus wenn File vorher existiert).

**Beispiele**

```
- name: run if /path/to/file does not exist
  command: /path/to/command with args creates=/path/to/file

# oder explizit mit args:
- name: cd to directory and run if /path/to/file does not exist
  command: /path/to/command with args
  args:
    chdir: /some/directory
    creates: /path/to/file
```

### Variablen

1. direkt im Playbook (`vars:`)
2. via Commandline (`ansible-playbook playbook.ymol --extra-vars="var1=value,var2=value"
3. in extra-File (`vars_files:`)
4. in der `hosts.ini` (`[multi:vars]`)

**📌 NOTE**\
Statt vieler Variablen im Inventory ist empfohlen, dort auf extra-Files `host_vars` und `group_vars` zu verweisen!

Variablen in einem extra-File sind alle am root-Level; z.B.:

```
# playbook.yml
---
- hosts: example

  vars_files:
    - vars.yml


# vars.yml
foo: bar
```

**Selektiver Import von Variablen**

```
# playbook.yml:

---
- hosts: example

  pre_tasks:
    - include_vars: {{ item }}"
      with_first_found:
        - "apache_{{ansible_os_family }}.yml
        - "apache_default.yml"
```

#### nested variables

Einfacher Zugriff über dot-Syntax; gefährlich bei speziellen Zeichen oder Whitespace. Sichere Variante über bracket-Syntax.

**Beispiel**

```
---
- hosts: all

  tasks:
    - debug:
        var: ansible_eth1

    - name: Ausgabe ipv4-Adresse mit dotted Syntax
      debug:
        msg: "Adresse: {{ ansible_eth1.ipv4.address }}"

    - name: Ausgabe der ipv4-Adresse mit bracket syntax (safer!)
      debug:
        msg: "Adresse: {{ ansible_eth1['ipv4']['address'] }}"
```

**variable precedence**

1. `--extra-vars`
2. task-level vars
3. block-level vars
4. role vars (z.B. in `<role>/vars/main.yml`, oder von `include_vars`
5. vars set via `set_facts`
6. vars set via `register`
7. play-level vars (1. `vars_files`, 2. `vars_prompt`, 3. `vars`
8. host facts
9. playbook host_vars
10. playbook group_vars
11. inventory (1. `host_vars`, 2. `group_vars`, 3. `vars`
12. role default vars (z.B. in `<role>/defaults/main.yml`)

### pre_tasks, post_tasks

Werden vor/nach anderes tasks oder roles aufgerufen

**Beispiel**

```
pre_tasks:
  - name: update cache if needed
    apt: update_cache=yes cach_valid_time=3600
```

### Handlers

Spez. Tasks, die **nach** den normalen Tasks aufgerufen werden - aber nur, wenn:

* einer der Tasks ein `notify` an den Handler auslöst
* dieser Task ohne Fehler durchläuft
* dieser Task eine Änderung gemacht hat

**📌 NOTE**\
Handlers werden nicht aufgerufen, wenn ein Playbook "failed"; falls dies nicht erwünscht ist: `ansible-playbook --force-handlers`

**Beispiel**

```
tasks:
  [...]
    notify: restart apache
[...]

handlers:
  - name: restart apache
    service: name=apache2 state=restarted
```

**Beispiel mit mehreren Handlers**

```
- name: rebuild app
  command: /some/command
  notify:
    - restart apache
    - restart memcached
```

**📌 NOTE**\
Ein Handler kann selbst mit `notify` einen weiteren Handler auslösen

### conditionals

Einfache Jinja-Funktionen sind vorzuziehen; komplexe Bedingungen über Python-Funktionen möglich ("built-ins")

**Beispiel mit Jinja**

```
- dnf: name=mysql-server state=present
  when: is_db_server

# oder, falls "is_db_server" nicht zwingend definiert ist:
  when: (is_db_server is defined) and is_db_server

# Beispiel mit parsen eines Outputs:
- command: my-app --status
  register: myapp_result

- command: do_something
  when: "'ready' in myapp_result.stout"

# Beispiel mit selektivem Kopieren eines Files
- stat: path=/etc/hosts
  register: hosts_file
- copy: src=pfadd dest=/etc/hosts
  when: hosts_file.stat.exists == false
```

Ansible kann nicht immer feststellen, ob ein Command etwas verändert -> verwendet man das `command` oder `shell`-Modul, geht Ansible immer von einer Veränderung aus. Man kann die Veränderung aber auch explizit mit `changed_when` (oder einen Fehler mit `failed_when`) übersteuern:

**Beispiel**

```
- name: installiere etwas
  command: "/pfad/zu_command"
  register: cmdoutput
  changed_when: "'Nothing to install' not in cmdoutput.stdout"
```

**Beispiel mit Python**

```
- name mach was nur mit Version 4 einer Software (Bsp: 4.6.1)
  [task...]
  when: software_version.split('.'[0]=='4'
```

## Ansible Vault

Jedes File kann alternativ im Vault abgelegt werden. Zur Verwendung muss das Entschüsselungspasswort angegeben werden.

**Beispiel**

```
ansible-vault encrypt vars/secrets.yml

ansible-playbook playbook.yml --ask-vault-pass

# oder mit einem PW-File außerhalb von git
ansible-playbook playbook.yml --vault-password-file /pfad/zu/pwfile

# verschlüsselte Files editieren:
ansible-vault edit vars/secrets.yml
```

Es kann auch ein Python-Script zum Erhalten des PW geschrieben werden (Wrapper f. Bitwarden-CLI?) -> s. ansible-vault Doku

## Roles

Rollen werden normalerweise in einem Unterordner `roles` neben dem Playbook erstellt. Leeres Gerüst für eine Rolle erstellen: `ansible-galaxy role init my_role`. Zwingend benötigt werden aber nur die Unterordner `roles/<rolename>/{meta,tasks\`.

Plattformabhängige Tasks/Variablen via Roles abstrahieren; z.B.

```
- name: include os-specific vars
  include_vars: {{ ansible_os_family }}.yml

- name: include os-specific setup tasks
  include_tasks: setup-{{ ansible_os_family }}.yml

- name: other os-agnostic tasks...
```

Die benötigten Vars/Tasks dann z.B. als `vars/Debian.yml` oder `tasks/setup-RedHat.yml` definieren.

## ansible-galaxy

siehe https://galaxy.ansible.com/ui/

```
ansible-galaxy role install geerlingguy.apache geerlingguy.mysql

# oder spezifische Version:
ansible-galaxy role install geerlingguy.apache,2.1.2

ansible-galaxy role list
ansible-galasy role remove <role>
```

Benötigte Rollen von Galaxy können auch in einem YAML definiert werden:

**`requirements.yml`**

```
---
roles:
  -name: geerlingguy.firewall

  - name: geerlingguy.php
    version: 4.3.1

  # andere Quellen statt ansible galaxy
  - src: https://github.com/geerlingguy/ansible-role-passenger
    name: passenger
    version: 2.0.0

  - src: https://example.com/ansible/roles/my-role.tar.gz
    name: my-role
```

Installieren der Rollen mit `ansible-galaxy install -r requirements.yml`

## Collections

Collections können Rollen, Playbooks und auch Plugins (in python geschrieben) bündeln.

## Diverses

**Kleinigkeiten**

1. Fehler ignorieren: `ignore_errors` beim jeweiligen Task
2. Playbooks können auch auf interaktiven Input warten (igitt) - siehe `vars_prompt`

### Delegierung

Tasks können auf andere Hosts (wie etwa die lokale Maschine) delegiert werden:

```
- name: remove from load balancer
  command: remove_from_lb {{ inventory_hostname }}
  delegate_to: 127.0.0.1

# speziell für localhost gibt es einen Shortcut:
- name: remove from load balancer
  local_action: command remove_from_lb {{ inventory_hostname }}
```

### Strukturierung

1. `import_tasks` zum statischen Import von Tasks/Handlern

   Können auch Variablen weitergeben; z.B.
   ```
   tasks:
     - import_tasks: user.yml
       vars:
         username: johndoe
         ssh_private_keys:
           - { src: /pfad, dest: id_rsa }
   ```

   Ebenfalls können Tags mitgegeben werden (`import_tasks: tasks/file.yml tags=blah`)!
2. `include_tasks` gehen auch dynamisch

   ```
   - name: check if present
     stat:
       path: /some/path
     register: myfile

   - include_tasks: /pfad/zu/task.yml
     when: myfile.stat.exists
   ```
3. `import_playbook`

**📌 NOTE**\
Best practice: Keine riesigen monolithischen Playbooks; mehrere Tasks zusammenfassen und mit `import_task` aufteilen!

### Tags

Mit Tags können komplexere Playbooks unterteilt werden; selektive Ausführung von z.B. allen Tasks mit einem Tag geht dann z.B. über `ansible-playbook playbook.yml --tags "tomcat,apache"`.

Man kann auch alles **außer** einem Tag ausführen (`ansible-playbook playbook.yml --skip-tags "postfix"`)

### Blocks

In Blocks können z.B. Tasks zusammengefasst werden, die alle Settings oder Conditionals teilen.

**Beispiel**

```
---
- hosts: web
  tasks:
    # RHEL
    - block:
      - dnf: name=https state=present
      [...]
    when: ansible_os_family == 'RedHat'
    become: yes

    # Debian
    - block:
      [...]
    when: ansible_os_family == 'Debian'
    become: yes
```

### Facts

Anzeigen: `ansible <ziel> -m setup`

Deaktvieren in einem Playbook mit `gather_facts: no`

Können auch auf einem remote-Host abgelegt werden (als ini-File, JSON-File, oder Command der JSON dynamisch produziert). Dazu die Files in `/etc/ansible/facts.d` erstellen.

### `lineinfile`

```
- name: adjust php setting
  lineinfile:
    dest: "/etc/php/8.2/apache2/conf.d/10-opcache.ini"
    regexp: "^opcache.memory_consumption"
    line: "opcache.memory_consumption = 96"
    state: present
  notify: restart apache
```

### `register`

Mit `register` können Werte in Variablen für spätere Verwendung gespeichert werden.

**Beispiel**

```
- name: get environment variable
  shell: 'source ~/.bash_profile && echo $ENV_VAR'
  register: foo

- name: print variable
  debug:
    msg: "Inhalt von foo: {{ foo.stout }}
```

**📌 NOTE**\
Buchstaben, Zahlen und `_` sind erlaubt; als best practice: all lowercase

### Environment-Variablen setzen

**Beispiel**

```
- name: download mit proxy
  get_url:
    url: http://example.com/file
    dest: ~/Downloads/
  environment:
    http_proxy: http://mein-proxy:80/
```

**Beispiel mit Variablen**

```
vars:
  proxy_vars:
    http_proxy: http://mein-proxy:80/
    https_proxy: https://mein-proxy:443/

tasks:
  - name: download mit proxy
    get_url:
      url: http://example.com/file
      dest: ~/Downloads/
    environment: proxy_vars
```

### YAML-Spezifika

* Folding

  Mit einem `>` initiiert man Folding - alle nachfolgenden gleich eingerückten Zeilen werden wie ein langer String behandelt (die Zeilen werden mit einem Leerzeichen getrennt)

  **(unschönes) Beispiel**

  ```
  tasks:
    - name: copy files
      command: >
        cp httpd.conf
        /etc/httpd/conf/httpd.conf
    - name: [...]
  ```
* weitere Yaml-spezifika...

### Parallele Ausführung
Ansible führt die Tasks parallel aus; die Reihenfolge entspricht damit nicht zwingend der der Hosts im Inventory.

Serielle Ausführung erzwingen: `ansible <gruppe> -a "<command>" -f 1`

### Fehlermeldung wg. discovery von python
siehe [Ansible Doku](https://docs.ansible.com/ansible-core/2.18/reference_appendices/interpreter_discovery.html)

-> `ansible_python_interpreter=auto_silent` im Inventory, oder `interpreter_python=auto_silent` in der `[defaults]'-Sektion in `ansible.cfg` (Orte: aktuelles Verz., `~/.ansible.cfg` oder `/etc/ansible/ansible.cfg`)

### `ansible.cfg`
Beim Projekt: `ansible-config init --disabled > ansible.cfg` (alle Def.werte disabled)

### ad-hoc-command vs. playbook
`ansible <gruppe> -b -m apt -a "name=chrony state=present"` entspricht folgendem Playbook:

```
---
- hosts: Gruppe
  become: yes

  tasks:
  - name: ensure chrony ist installed
    apt:
      name: chrony
      state: present
```
