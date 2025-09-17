#  How to completely uninstall git from Ubuntu 

To remove Git from Ubuntu, the method depends on how Git was initially installed.

## 1. If Git was installed using the package manager (APT):
This is the most common and recommended way to remove Git. Remove the Git package.
Code
```bash
    sudo apt remove git
```

This command removes the git package, but may leave some configuration files.
Remove the Git package and its configuration files (purge):
Code
```bash
    sudo apt purge git

```  
This command removes the git package along with its configuration files.
Remove automatically installed dependencies no longer needed:
Code

```bash

    sudo apt autoremove
```
This command removes any packages that were installed as dependencies for Git and are no longer required by other installed software.

## 2. If Git was installed from source:
If Git was compiled and installed from its source code, the removal process is different. Navigate to the Git source directory.
Change your current directory to the location where you compiled Git. Run make uninstall (if available).
Code
```bash
    sudo make uninstall
```

If the Git source code includes an uninstall target in its Makefile, this command should remove the installed files.
Manually remove files (if make uninstall is not available or incomplete):
If make uninstall is not an option or doesn't fully remove Git, you may need to manually remove the files that were installed. This typically involves deleting files from directories like /usr/local/bin, /usr/local/share/man, etc., where Git would have placed its executables and documentation. You can use tools like installwatch (or checkinstall) during the initial installation to track installed files for easier removal.

## 3. Remove user-specific Git configuration:
Regardless of how Git was installed, your personal Git configuration is usually stored in your home directory. Remove the .gitconfig file.
Code
```bash
    rm ~/.gitconfig
```
This removes your global Git configuration settings.
Remove any local .git directories within repositories:
If you have local Git repositories, each will contain a hidden .git directory. To remove these, navigate to the root of the repository and delete the .git directory:
Code
```bash
    rm -rf .git
```
Caution: This action permanently deletes the Git history and tracking for that specific repository. Only do this if you intend to completely remove the repository's Git management.