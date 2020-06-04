.. role:: raw-html-m2r(raw)
   :format: html


**Private Registry auf dem Installserver nutzen (nexus3-sonatype)**

Zur Nutzung eines privaten Repositories für Docker-Images und für andere Dateien wurde auf dem Installserver ein **Nexus Repository-Manager** eingerichtet.

Dieser Manager ist auf dem Installserver erreichbar unter der Adresse [https://10.120.13.250:8443}(https://10.120.13.250:8443).

Der Admin-Account wird mit dem user “admin” erreicht.

Für die Verbindung von einem Rechner zum Repository Manager sind noch folgende Einträge vonnöten:

.. code-block:: shell

   [root@demo01 ~]# cat /etc/containers/registries.conf
   # This is a system-wide configuration file used to
   # keep track of registries for various container backends.
   # It adheres to TOML format and does not support recursive
   ...
   [registries.search]
   registries = ['registry.access.redhat.com', 'docker.io', 'registry.fedoraproject.org', 'quay.io', 'registry.centos.org']

   # If you need to access insecure registries, add the registry's fully-qualified name.
   # An insecure registry is one that does not have a valid SSL certificate or only does HTTP.
   [registries.insecure]
   registries = ['installserver:5080']

Bitte vorher noch sicherstellen, dass der Eintrag installserver auf die ip 10.120.13.250 in der hosts-Datei zeigt.

zusätzlich benötigt es für den Docker-Client noch folgenden Eintrag:

.. code-block:: shell

   [root@demo01 docker]# cat /etc/docker/daemon.json    (Datei ggf. neu anlegen root:root)
   {
     "insecure-registries" : ["installserver:5080"]
   }

Den Docker daemon anschließend neu starten: ``# systemctl restart docker``.

Jetzt kann mit dem Kommando ``# docker login  installserver:5080``  ein unverschlüsselter login erfolgen. Daraufhin legt der Client eine Datei ~/.docker/config.json an mit einem Token, der dann für weitere Docker Transaktionen wie ``docker push`` benötigt wird.

Ausloggen kann man sich wieder mit ``# docker logout installserver:5080``.

Die eingerichteten User auf dem Repository-Server lauten: **student1 - student8**\ , jeweils **pw: student123**

Beispiel um ein Image herunterzuladen:

.. code-block:: shell

   [root@demo01 .docker]# docker login installserver:5080
   Username: student2
   Password:
   Login Succeeded

Download eines Images mit dem Namen mynginx:rev01 vom Installserver:

.. code-block:: shell

   [root@demo01 .docker]# docker pull installserver:5080/mynginx:rev01
   Trying to pull repository installserver:5080/mynginx ...
   rev01: Pulling from installserver:5080/mynginx
   1ab2bdfe9778: Pull complete
   a17e64cfe253: Pull complete
   e1288088c7a8: Pull complete
   Digest: sha256:099019968725f0fc12c4b69b289a347ae74cc56da0f0ef56e8eb8e0134fc7911
   Status: Downloaded newer image for installserver:5080/mynginx:rev01

Mit den Kommandos:

.. code-block:: shell

   # docker tag <original_imagename:tag>  installserver:5080/studentXY/<imagename:tag>
   # docker push installserver:5080/studentXY/<imagename:tag>

kann ein neues Image ins Unterverzeichnis mit dem Namen :raw-html-m2r:`<studentXY>` des jeweiligen Users und dem entsprechenden Imagename:tag hochgeladen werden. Das Userverzeichnis wird automatisch angelegt, sofern es noch nicht vorhanden ist.

Beispiel:

.. code-block:: shell

   [root@demo01 .docker]# docker tag installserver:5080/mynginx:rev01 installserver:5080/student2/mynginx:rev01

   [root@demo01 .docker]# docker push installserver:5080/student2/mynginx:rev01
   The push refers to a repository [installserver:5080/student2/mynginx]
   12fdf55172df: Layer already exists
   002a63507c1c: Layer already exists
   1c95c77433e8: Layer already exists
   rev01: digest: sha256:099019968725f0fc12c4b69b289a347ae74cc56da0f0ef56e8eb8e0134fc7911 size: 948
   [root@demo01 .docker]#

Hochgeladene Images sollten sich bei richtiger Pfadangabe auch als user anonymous, d.h. ohne vorheriges docker login herunterladen lassen.

Damit auch verschlüsselte Verbindungen vom Docker client aus möglich sind, benötigt man ein Zertifikat vom Server. Dieses liegt auf dem Installserver im Verzeichnis: ``/opt/nexus/etc`` und der Dateiname lautet: **ca.crt**.

Man kann den Inhalt mit ``scp`` oder mit copy und paste auf die lokale Maschine in ein neues Verzeichnis mit dem Namen ``# /etc/docker/certs.d/installserver:5443`` anlegen.

Der Dateiname lautet dann **ca.crt**

.. code-block:: shell

   [root@demo01 installserver:5443]# pwd
   /etc/docker/certs.d/installserver:5443

   [root@demo01 installserver:5443]# cat ca.crt
   -----BEGIN CERTIFICATE-----
   MIIDVzCCAj+gAwIBAgIEc6PEsjANBgkqhkiG9w0BAQsFADBFMRQwEgYKCZImiZPy
   LGQBGRYEc2l0ZTEVMBMGCgmSJomT8ixkARkWBWxvY2FsMRYwFAYDVQQDEw1pbnN0
   YWxsc2VydmVyMB4XDTE5MDgyMjA5MDYwNVoXDTQxMDcxNzA5MDYwNVowRTEUMBIG
   CgmSJomT8ixkARkWBHNpdGUxFTATBgoJkiaJk/IsZAEZFgVsb2NhbDEWMBQGA1UE
   AxMNaW5zdGFsbHNlcnZlcjCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB
   AMdvCTHxb3uX5vwDTAtLjUvGkvcWDHR2wxUF1afIf32+rvLJ+0YsunR8WPvJNVWs
   Waly1cahGyhndLlRJ8OSRclS/axZz5as15W3pG9Vg0kIDDskKtmUG7o69HTBkzQO
   mhRFyRcNg5BNPu44MEkoVgZy5TlJT/awmDfdaI4GsTyqGQ3ZKyrFuY3svjvrZw5p
   hFBZ/v9YZQVmIHWH0M7d9yYF3oy1u+zdBaKSlqNbommvbNp8eJf3WA4wPRyzO/zo
   L/Vp9DRy7NjXGyLXIkJ8fimAwvAH7jtsXz91t2umeSBWPyKu4zqfcTBNuTogwgYR
   zGoiOG82jul1fVEa6BwdwQcCAwEAAaNPME0wDAYDVR0TBAUwAwEB/zAeBgNVHREE
   FzAVgg1pbnN0YWxsc2VydmVyhwQKeA36MB0GA1UdDgQWBBTPPXmC6nCoWU7JX6BD
   h/VVc6RuvjANBgkqhkiG9w0BAQsFAAOCAQEANEf8GgkraqVmt/5KA5Aq7oOJRFD7
   isCDVg293Ij37NzGLIx08lxEgCbKEgXKqUK0qAxAN6s/m44DeM1+A3PDOxT9WMRA
   FVqpmGG3pv9V9UJlZ2U/Mi3BBN2JdkXTY2C4HMUt0kUPztETv9HVrVvnTRNVEhbn
   1lQDTv97+RFvhveXCgaWjYQM5u/Edt0NsvDmvo7BcT+CIXl7Wltwi9rF1LPYpiIS
   qDhDwku5U6AE8sNbW6MSAeJ+eqX3KfxJlevU+TIymvNBhMRLR7jKgAVUJ/HX8zsL
   20r+VbMtxxUcdJDDQbfmSxuLo2MdzV3ZU+t3OC34wNuJvzllUsUzHDjZEw==
   -----END CERTIFICATE-----
   ````shell

   Nun kann der verschlüsselte docker login erfolgen mit:
   ````shell
   # docker login installserver:5443     statt mit :5080

Alternativ, sofern installiert, lassen sich die Images auch mit ``podman`` herunterladen und genauso auch pushen, nur muss man dann mit ``podman login`` arbeiten.

**UPLOAD von Dateien auf den Installserver ins RAW-Repository**

Mit folgendem Kdo. kann man Dateien in das RAW-Repository hochladen. Die Option -k ist für das “ignorieren” des self-signed Zertifikates.

.. code-block:: shell

   # curl -v -k --user 'studentx:student123' --upload-file ./flaskdemo.tar https://installserver:8443/repository/schulung-raw/01-dockerimage/flaskdemo.tar
