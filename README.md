# Steps:

1. Create a jenkins job with the pipeline file in this repo. Check the
   environment variables and set their values based on where dashboard is
   hosted.
2. Create Jenkins Username-Passowrd Credential with id "dashboard-ssh-login":
   Settings > Credentials > System > Global Credentials.

3. Start jenkins server: "systemctl start jenkins"

4. Note, some systems might be blocking tcp on port 8080 used by jenkins UI, so enable it:

  ```sh
  # for fedora
  sudo firewall-cmd --permanent --add-port=8080/tcp
  sudo firewall-cmd --permanent --reload
  ```
5. Add jenkins to docker group, this is needed since the ci-scripts project uses docker to build:

  ```sh
  sudo usermod -aG docker jenkins
  # Restart is needed
  sudo systemctl restart jenkins
  ```

6. Add this to sudoers so jenkins can install tools needed to build the iso for booting:

  ```sh
  sudo visudo
  # add this line and save the editor and exit
    jenkins ALL=(root) NOPASSWD: /usr/bin/dnf -y install cloud-utils genisoimage
  
  # then restart
  sudo systemctl restart jenkins
  ```
