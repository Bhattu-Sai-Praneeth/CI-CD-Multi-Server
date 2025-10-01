# CI-CD-Multi-Server Configuration using Ansible


## 1. Introduction
This document explains how to install Apache Tomcat 10.1.45 on remote Linux machines using Ansible, deploy configuration files (`tomcat-users.xml` and `context.xml`) via Ansible templates, and configure the Tomcat Manager application.

---

## 2. Prerequisites
- Remote hosts with Linux (Amazon Linux, RHEL, CentOS, or similar)
- Ansible installed on the control machine
- Internet access on remote hosts to download Tomcat and Java
- Basic understanding of YAML, XML, and Ansible playbooks

---

## 3. Directory Structure

Place your playbook and configuration files in a single directory as shown:

```
/root/tomcat-setup/
├── tomcat.yml             # Ansible playbook
├── apache-tomcat-10.1.45-deployer.tar.gz
├── tomcat-users.xml       # Tomcat users configuration
├── context.xml            # Manager context configuration
├── sh.sh                  # optional helper script
```



## 4. Configuration Files

### tomcat-users.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">

  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="sai" password="sai" roles="manager-gui,manager-script"/>

</tomcat-users>
```

### context.xml (Manager Webapp)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context antiResourceLocking="false" privileged="true">
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```

---

## 5. Step-by-Step Instructions
1. Place `tomcat.yml`, `tomcat-users.xml`, and `context.xml` in the same directory.
2. Run the playbook:
   ```bash
   ansible-playbook -i inventory tomcat.yml
   ```
3. Verify that Tomcat is installed under `/root/tomcat` (or your defined `tomcat_install_dir`).
4. Ensure `tomcat-users.xml` is in `/root/tomcat/conf/` and `context.xml` is in `/root/tomcat/webapps/manager/META-INF/`.

---

## 6. Optional Notes and Best Practices
- **File Permissions:** By default, the files are copied with the user running Ansible. For production, you may want to set `owner`, `group`, and `mode`.
- **Using `.j2` Templates:** If you want dynamic usernames, passwords, or variables, rename files to `.j2` and use `{{ variable_name }}` placeholders.
- **Security:** Change default credentials (`sai/sai`) for production environments.
- **Tomcat User:** If Tomcat runs under a non-root user, adjust `owner` and `group` accordingly.

---

## 7. Starting and Verifying Tomcat
1. Start Tomcat manually:
```bash
cd /root/tomcat/bin
./startup.sh
```

2. Check if Tomcat is running:
```bash
ps -ef | grep tomcat
```

3. Access the Manager App:
```
http://<server-ip>:8080/manager/html
```
Login using `sai` / `sai` credentials.

4. Stop Tomcat if needed:
```bash
./shutdown.sh
```

---


