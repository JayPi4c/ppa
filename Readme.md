https://assafmo.github.io/2019/05/02/ppa-repo-hosted-on-github.html

or

https://github.com/marketplace/actions/github-pages-apt-repo

also

https://earthly.dev/blog/creating-and-hosting-your-own-deb-packages-and-apt-repo/


```bash
curl -s --compressed "https://jaypi4c.github.io/ppa/ubuntu/KEY.gpg" | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/jaypi4c.gpg >/dev/null
sudo curl -s --compressed -o /etc/apt/sources.list.d/jaypi4c.list "https://jaypi4c.github.io/ppa/ubuntu/jaypi4c.list"
sudo apt update
```



# Creating a PPA hosted with GitHub Pages

This repository is used to host a PPA for all my projects. If a `.deb` file is pushed to this repository, it will be automatically added to the PPA.

To set up this repository, the following steps are required:
1. Create a new repository on GitHub
2. Create a new GPG key with `gpg --full-gen-key` (RSA, 4096 bits, no expiry date) (note: currently it's not supported to have a passphrase on the key)
3. Export the private key using `gpg --export-secret-keys "${EMAIL}" | base64 > private.key`
   
   !!DO NOT ADD THE PRIVATE KEY TO THE REPOSITORY!!
4. Create a new secret in the repository settings with the name `GPG_SIGNING_KEY` and the value of the exported private key
5. Create the public key for the repository using `gpg --armor --export "${EMAIL}" > KEY.gpg`
6. Now create the `Packages` file using
```bash
dpkg-scanpackages --multiversion . > Packages
gzip -k -f Packages
```
7. Creating the `Release`, `Release.gpg` and `InRelease` files
```bash
apt-ftparchive release . > Release
gpg --default-key "${EMAIL}" -abs -o - Release > Release.gpg
gpg --default-key "${EMAIL}" --clearsign -o - Release > InRelease
```

8. In the final step it is required to create a `list` file
```bash
echo "deb [signed-by=/etc/apt/trusted.gpg.d/my_ppa.gpg] https://${GITHUB_USERNAME}.github.io/my_ppa ./" > my_list_file.list
```

If you want to change names of files or folders, make sure to change them in the whole repository.


This tutorial combines information from the following sources:
- https://assafmo.github.io/2019/05/02/ppa-repo-hosted-on-github.html
- https://stackoverflow.com/a/61748039


## Adding a new package to the PPA

This repository is configured to automatically deploy all `.deb` files that are pushed to the `main` branch. To add a new package to the PPA, simply push the `.deb` file to the `main` branch and it will be automatically added to the PPA. This can be done automatically using GitHub Actions. Refer to the following repository for an example:
- https://github.com/JayPi4c/ExampleDebianPackage