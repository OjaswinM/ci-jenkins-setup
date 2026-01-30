# Steps:

1. Create a jenkins job with the pipeline file
2. Some systems might be blocking tcp on port 8080 for jenkins so enable it:

  ```sh
  # for fedora
  sudo firewall-cmd --permanent --add-port=8080/tcp
  sudo firewall-cmd --permanent --reload
  ```
3. Add jenkins to docker, this is needed since the ci-scripts project uses docker to build:

  ```sh
  sudo usermod -aG docker jenkins
  # Restart is needed
  sudo systemctl restart jenkins
  ```

4. Add this to sudoers so jenkins can install tools needed to build the iso for booting:

```sh
sudo visudo
# add this line and save the editor and exit
  jenkins ALL=(root) NOPASSWD: /usr/bin/dnf -y install cloud-utils genisoimage

# then restart
sudo systemctl restart jenkins
```
